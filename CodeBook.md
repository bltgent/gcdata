Codebook for Getting and Cleaning Data Project

Variables:
1) subject - The number refers to the subject that gathered the data.
2) action - The action taking place at the time of measurement.
3) [measurement means] - variables number 3-50 are various measurements.
	
Data:
Data was gathered from files containing measurements from mobile devices worn by subjects.
Additional data used in the analysis came from descriptive documents as well as basic lists of
activity names and different measurement names.

Transformations:
Train/Test data: this data was only transformed at the end to determine the mean of each subject
and activity.
Features data was transformed to remove hyphens, parentheses, and to make all characters lower case.
Activity data was transformed to reveal the actual activity versus a number.

The data was arranged into a 50 variable dataframe.
It was then grouped according to subject and activity.
Then each variable was averaged based on each subject and activity.

The resulting data frame has dimensions (180,50)