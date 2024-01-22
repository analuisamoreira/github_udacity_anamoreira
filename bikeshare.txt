import time
import pandas as pd
import numpy as np

CITY_DATA = { 'chicago': 'chicago.csv',
              'new york city': 'new_york_city.csv',
              'washington': 'washington.csv' }

def get_filters():
    """
    Asks user to specify a city, month, and day to analyze.

    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello! Let\'s explore some US bikeshare data!')
    # TO DO: get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    
    while True:
        city = input("Please choose the city: Chicago, New York City or Washington?\n\n")
        # make entries case-insensitive and use small letters, as applied when assigning city names at lines 7-9
        city = city.lower()
        
        # invalid input handling for city
        if city not in ('new york city', 'chicago', 'washington'):
            print("Please enter a valid city.")
            continue
        else:
            break

    # get user input for month (all, january, february, ... , june)
    while True:
        month = input("Choose the month between January, February, March, " +
                      "April, May and June, or type all if you do not wish "+
                      "to specify a month.\n\n")
        # make entries case-insensitive and use small letters
        month = month.lower()
        
        # invalid input handling for month
        if month not in ('january', 'february', 'march', 'april', 'may', 
                         'june', 'all'):
            print("Please enter a valid month.")
            continue
        else:
            break

    # get user input for day of week (all, monday, tuesday, ... sunday)
    while True:
        day = input("Choose the day between Monday, Tuesday, Wednesday," +
                    "Thursday, Friday, Saturday or Sunday, or type all if " +
                    "you do not wish to specify a day.\n\n")
        # make entries case-insensitive and use small letters
        day = day.lower()
        
        # invalid input handling for month
        if day not in ('monday', 'tuesday', 'wednesday', 'thursday', 'friday',
                       'saturday', 'sunday', 'all'):
            print("Please enter a valid day.")
            continue
        else:
            break

    print('-'*40)
    return city, month, day


def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.

    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
    df=pd.read_csv(CITY_DATA[city])
    # Convert 'Start Time' to datetime format
    df['Start Time'] = pd.to_datetime(df['Start Time'])
    # Extract month and create a new column 'Month'
    df['Month'] = df['Start Time'].dt.month
    # Extract day of the week and create a new column 'Day of Week'
    df['Day of Week'] = df['Start Time'].dt.day_name()
     #Extract hour from Start Time column:
    df['hour'] = df['Start Time'].dt.hour
    
    #Filter by month if the user doesn't choose the "all" option
    if month != 'all':
        # Use the index of the months list to get the corresponding int
        months = ['january', 'february', 'march', 'april', 'may', 'june']
        month = months.index(month) + 1
        # Filter by month to create the new dataframe
        df = df[df['Month'] == month]

    # Filter by day of week if applicable
    if day != 'all': 
        # Filter by day of week to create the new dataframe
        df = df[df['Day of Week'].str.lower() == day.lower()]
    
    return df


def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()
    
    # TO DO: display the most common month
    most_common_month = df['Month'].value_counts().idxmax()
    # Change number to month:
    if most_common_month == 1:
        most_common_month = "january"
    elif most_common_month == 2:
        most_common_month = "february"
    elif most_common_month == 3:
        most_common_month = "march"
    elif most_common_month == 4:
        most_common_month = "april"
    elif most_common_month == 5:
        most_common_month = "may"
    elif most_common_month == 6:
        most_common_month = "june"   
    print(f"The most common month is: {most_common_month}")
    # TO DO: display the most common day of week
    most_common_day = df['Day of Week'].value_counts().idxmax()
    print(f"The most common day of week is: {most_common_day}")
    # TO DO: display the most common start hour
    most_common_hour = df['hour'].value_counts().idxmax()
    print(f"The most common start hour is: {most_common_hour}")

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

    
def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # TO DO: display most commonly used start station
    most_common_start_station = df['Start Station'].mode().iloc[0]
    print(f"The most commonly used start station is: {most_common_start_station}")

    # TO DO: display most commonly used end station
    most_common_end_station = df['End Station'].mode().iloc[0]
    print(f"The most commonly used end station is: {most_common_end_station}")

    # TO DO: display most frequent combination of start station and end station trip
    # 1. combine start and end stations into new column 
    df['Station Combination'] = df['Start Station'] + ' (start) and ' + df['End Station'] + ' (end).'
    # 2. print station combination
    print('The most common station combination for your selection is ', 
          df['Station Combination'].mode().iloc[0])

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # TO DO: display total travel time
    total_travel_time = df['Trip Duration'].sum()/3600
    print(f'Total travel time is equal to {total_travel_time} hours')
    # TO DO: display mean travel time
    mean_travel_time = df['Trip Duration'].mean()/60
    print(f'The mean travel time is equal to {mean_travel_time} minutes')
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()
    
    # TO DO: Display counts of user types
    # Check if 'User Type' column exists in the DataFrame
    if 'User Type' in df.columns:
        # Display counts of user types
        count_user_types = df['User Type'].value_counts()
        print(f'The counts of user types are: {count_user_types}.')
    else:
        print('User Type information is not available for this dataset.')
    
    # TO DO: Display counts of gender
    # Check if 'Gender' column exists in the DataFrame
    if 'Gender' in df.columns:
        # Display counts of gender
        count_gender = df['Gender'].value_counts()
        print(f'The counts of gender are:\n{count_gender}')
    else:
        print('Gender information is not available for this dataset.')

    # TO DO: Display earliest, most recent, and most common year of birth 
    # Check if 'Birth Year' column exists in the DataFrame
    
    if 'Birth Year' in df.columns:
        earliest_birth_year = df['Birth Year'].min()
        most_recent_birth_year = df['Birth Year'].max() 
        most_common_birth_year = df['Birth Year'].mode().iloc[0]
        
        print(f'Earliest birth year: {earliest_birth_year}')
        print(f'Most recent birth year: {most_recent_birth_year}')
        print(f'Most common birth year: {most_common_birth_year}')
       
    else:
        print('Birth Year information is not available for this dataset.')
   
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

    
def display_data(df):
    """
    Displays rows of data upon user request.

    Args:
        df - Pandas DataFrame containing the data.
    """
    view_data = input("Would you like to view 5 rows of individual trip data? Enter yes or no: ").lower()
    start_loc = 0

    while view_data == 'yes':
        print(df.iloc[start_loc:start_loc + 5])
        start_loc += 5
        view_data = input("Do you wish to continue? Enter yes or no: ").lower()

        
    
def main():
    while True:
        city, month, day = get_filters()
        print("You selected {}, {}, and {}.".format(city.title(), month.title(), day.title()))
        df = load_data(city, month, day)
        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)
        display_data(df)
        restart = input('\nWould you like to restart? Enter yes or no.\n')
        if restart.lower() != 'yes':
            break


if __name__ == "__main__":
	main()

