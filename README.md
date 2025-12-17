# Movie Dashboard
## Table of Contents:
- [Problem Statement](#problem-statement)
- [Data Source](#data-source)
- [Tools](#tools)
- [Data Cleaning](#data-cleaning)
- [M Code](#m-code)
- [Recommendations](#recommendations)
### Problem Statement
Netflix aims to better understand which movie it should produce next, including the most suitable actors and directors. We have a dataset containing movie budgets, box office performance, actors, directors, and genres. Your task is to build an Excel dashboard that provides insights into this dataset. The dashboard should help identify:
- The best-performing actors
- The top movies based on box office metrics
- Director performance
- Genre trends
- Seasonal patterns in movie performance
- Any additional insights that can guide future production decisions
  
The final dashboard should be clear, interactive, and visually compelling, enabling Netflix to make data-driven decisions.
### Data Source
Movie Data: The primary dataset used for this analysis is the "Movie Data Homework.xlsx" file, containing detailed information about each movie's performance (box office and budget), actors, directors, and genres. You can download the original data source here: [Movie Dataset Excel file](https://github.com/Anburmist/MovieProject/blob/main/Movies_Data%20_Dashboard.xlsx)
### Tools
1. Power Query - I used Power Query for Data Cleaning
2. Excel - I used Excel for Data Analysis
3. Pivot Tables - for Creating the dashboard and Visualizations
### Data Cleaning
- Data loading and inspection.
- Handling errors, missing values.
- Data cleaning and formatting. The Excel file after the data cleaning & preparation process can be downloaded here - [Movies 
Dashboard](https://github.com/Anburmist/MovieProject/blob/main/Movie%20Data%20Project%20_%20Complete.xlsx)
### Dashboard
<img width="825" height="620" alt="Screenshot 2025-12-16 at 9 38 18 PM" src="https://github.com/user-attachments/assets/bd88beb5-217f-4a36-b4f5-4581e50d7a3a" />

### M Code
```
let
  Source = Excel.Workbook(File.Contents("/Users/annaburm/Downloads/Movies_Data_Homework (1).xlsx"), null, true),
  #"Navigation 1" = Source{[Item = "Movie Data", Kind = "Sheet"]}[Data],
  #"Promoted headers" = Table.PromoteHeaders(#"Navigation 1", [PromoteAllScalars = true]),
  #"Changed column type" = Table.TransformColumnTypes(#"Promoted headers", {{"Movie Title", type text}, {"Release Date", type date}, {"Wikipedia URL", type text}, {"Genre_First_ID", Int64.Type}, {"Genre_Second_ID", Int64.Type}, {"Director_First_ID", Int64.Type}, {"Cast_First_ID", Int64.Type}, {"Cast_Second_ID", Int64.Type}, {"Cast_Third_ID", Int64.Type}, {"Cast_Fourth_ID", Int64.Type}, {"Cast_Fifth_ID", Int64.Type}, {"Budget ($)", Int64.Type}, {"Box Office Revenue ($)", type number}}),
  #"Removed columns" = Table.RemoveColumns(#"Changed column type", {"Column14", "Column15", "Column16", "Column17", "Column18", "Column19", "Column20", "Column21"}),
  #"Changed column type 1" = Table.TransformColumnTypes(#"Removed columns", {{"Genre_First_ID", Int64.Type}, {"Genre_Second_ID", Int64.Type}}),
  #"Merged queries" = Table.NestedJoin(#"Changed column type 1", {"Director_First_ID"}, Directors, {"ID"}, "Directors", JoinKind.LeftOuter),
  #"Expanded Directors" = Table.ExpandTableColumn(#"Merged queries", "Directors", {"Director"}, {"Director"}),
  #"Merged queries 1" = Table.NestedJoin(#"Expanded Directors", {"Cast_First_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors" = Table.ExpandTableColumn(#"Merged queries 1", "Actors", {"Actor"}, {"Actor"}),
  #"Reordered columns" = Table.ReorderColumns(#"Expanded Actors", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_Second_ID", "Director_First_ID", "Cast_First_ID", "Actor", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)", "Director"}),
  #"Merged queries 2" = Table.NestedJoin(#"Reordered columns", {"Cast_First_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors 1" = Table.ExpandTableColumn(#"Merged queries 2", "Actors", {"Actor"}, {"Actor.1"}),
  #"Merged queries 3" = Table.NestedJoin(#"Expanded Actors 1", {"Cast_Second_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors 2" = Table.ExpandTableColumn(#"Merged queries 3", "Actors", {"Actor"}, {"Actor.2"}),
  #"Renamed columns" = Table.RenameColumns(#"Expanded Actors 2", {{"Actor.2", "Cast 2"}, {"Actor.1", "Cast 1"}}),
  #"Merged queries 4" = Table.NestedJoin(#"Renamed columns", {"Cast_Third_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Merged queries 5" = Table.NestedJoin(#"Merged queries 4", {"Cast_Fourth_ID"}, Actors, {"ID"}, "Actors (2)", JoinKind.LeftOuter),
  #"Merged queries 6" = Table.NestedJoin(#"Merged queries 5", {"Cast_Fifth_ID"}, Actors, {"ID"}, "Actors (3)", JoinKind.LeftOuter),
  #"Renamed columns 1" = Table.RenameColumns(#"Merged queries 6", {{"Actors", "Cast 3"}, {"Actors (2)", "Cast 4"}, {"Actors (3)", "Cast 5"}}),
  #"Expanded Cast 3" = Table.ExpandTableColumn(#"Renamed columns 1", "Cast 3", {"Actor"}, {"Actor.1"}),
  #"Expanded Cast 4" = Table.ExpandTableColumn(#"Expanded Cast 3", "Cast 4", {"Actor"}, {"Actor.2"}),
  #"Expanded Cast 5" = Table.ExpandTableColumn(#"Expanded Cast 4", "Cast 5", {"Actor"}, {"Actor.3"}),
  #"Merged queries 7" = Table.NestedJoin(#"Expanded Cast 5", {"Genre_First_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
  #"Merged queries 8" = Table.NestedJoin(#"Merged queries 7", {"Genre_Second_ID"}, Genres, {"ID"}, "Genres (2)", JoinKind.LeftOuter),
  #"Renamed columns 2" = Table.RenameColumns(#"Merged queries 8", {{"Genres", "Genre 1"}, {"Genres (2)", "Genre 2"}}),
  #"Expanded Genre 1" = Table.ExpandTableColumn(#"Renamed columns 2", "Genre 1", {"Genre"}, {"Genre"}),
  #"Expanded Genre 2" = Table.ExpandTableColumn(#"Expanded Genre 1", "Genre 2", {"Genre"}, {"Genre.1"}),
  #"Renamed columns 3" = Table.RenameColumns(#"Expanded Genre 2", {{"Actor.1", "Cast 3"}, {"Actor.2", "Cast 4"}, {"Actor.3", "Cast 5"}}),
  #"Reordered columns 1" = Table.ReorderColumns(#"Renamed columns 3", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre.1", "Director_First_ID", "Cast_First_ID", "Actor", "Cast_Second_ID", "Cast 2", "Cast_Third_ID", "Cast 3", "Cast_Fourth_ID", "Cast 4", "Cast_Fifth_ID", "Cast 5", "Budget ($)", "Box Office Revenue ($)", "Director", "Cast 1"}),
  #"Removed columns 1" = Table.RemoveColumns(#"Reordered columns 1", {"Cast 1"}),
  #"Added custom" = Table.AddColumn(#"Removed columns 1", "ROI", each ([#"Box Office Revenue ($)"]-[#"Budget ($)"])/[#"Budget ($)"]),
  #"Changed column type 2" = Table.TransformColumnTypes(#"Added custom", {{"ROI", Percentage.Type}})
in
  #"Changed column type 2"

```
### Recommendations
Top 5 genres are Action, Comedy, etc. I would recommend Netflix to produce a movie with one of these genres as they brought in more at the box office revenue based on the data from 2012 to 2016

<img width="525" height="195" alt="Screenshot 2025-12-16 at 9 44 13 PM" src="https://github.com/user-attachments/assets/c6201e62-eba9-41b9-8329-7a56e5d12b66" />
