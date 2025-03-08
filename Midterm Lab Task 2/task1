let
    Source = Excel.Workbook(File.Contents("C:\Users\COMLAB\Downloads\Uncleaned_DS_jobs.xlsx"), null, true),
    Uncleaned_DS_jobs_Sheet = Source{[Item="Uncleaned_DS_jobs",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(Uncleaned_DS_jobs_Sheet, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"index", Int64.Type}, {"Job Title", type text}, {"Salary Estimate", type text}, {"Job Description", type text}, {"Rating", type number}, {"Company Name", type text}, {"Location", type text}, {"Headquarters", type any}, {"Size", type any}, {"Founded", Int64.Type}, {"Type of ownership", type any}, {"Industry", type any}, {"Sector", type any}, {"Revenue", type any}, {"Competitors", type any}}),
    #"Extracted Text Before Delimiter" = Table.TransformColumns(#"Changed Type", {{"index", each Text.BeforeDelimiter(Text.From(_, "en-PH"), "("), type text}}),
    #"Inserted Addition" = Table.AddColumn(#"Extracted Text Before Delimiter", "Min Sal", each Number.From([index]) + 101, type number),
    #"Inserted Text Between Delimiters" = Table.AddColumn(#"Inserted Addition", "Max Sal", each Text.BetweenDelimiters([Salary Estimate], "$", " ", 1, 0), type text),
    #"Added Custom" = Table.AddColumn(#"Inserted Text Between Delimiters", "Role Type", each if Text.Contains([Job Title], "Data Scientist") then
"Data Scientist"
else if Text.Contains([Job Title], "Data Analyst") then
"Data Analyst"
else if Text.Contains([Job Title], "Data Engineer") then
"Data Engineer"

else if Text.Contains([Job Title], "Machine Learning") then
"Machine Learning Engineer"
else
"other"),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"Role Type", type text}}),
    #"Added Custom1" = Table.AddColumn(#"Changed Type1", "Custom", each if [Location]= "New Jersey" then ", NJ"
else if [Location] = "Remote" then ", other"
else if [Location]= "United States" then ", other"
else if [Location]= "Texas" then ", TX"
else if [Location]= "Patuxent" then ", MA"
else if [Location]= "California" then ", CA"
else if [Location]= "Utah" then ", UT"
else [Location]),
    #"Split Column by Delimiter" = Table.SplitColumn(#"Added Custom1", "Custom", Splitter.SplitTextByDelimiter(",", QuoteStyle.Csv), {"Custom.1", "Custom.2"}),
    #"Changed Type2" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Custom.1", type text}, {"Custom.2", type text}}),
    #"Renamed Columns" = Table.RenameColumns(#"Changed Type2",{{"Custom.1", "Location Correction Custom.1"}, {"Custom.2", "Location Correction Custom.2"}}),
    #"Replaced Value" = Table.ReplaceValue(#"Renamed Columns","Anne Rundell","MA",Replacer.ReplaceText,{"Location Correction Custom.2"}),
    #"Renamed Columns1" = Table.RenameColumns(#"Replaced Value",{{"Location Correction Custom.2", "State Abbreviations"}}),
    #"Inserted Text Before Delimiter" = Table.AddColumn(#"Renamed Columns1", "MinCompanySize", each Text.BeforeDelimiter([Size], " "), type text),
    #"Inserted Text Between Delimiters1" = Table.AddColumn(#"Inserted Text Before Delimiter", "MaxCompanySize", each Text.BetweenDelimiters([Size], " ", " ", 1, 0), type text),
    #"Split Column by Delimiter1" = Table.SplitColumn(#"Inserted Text Between Delimiters1", "MinCompanySize", Splitter.SplitTextByDelimiter("-", QuoteStyle.Csv), {"MinCompanySize.1", "MinCompanySize.2"}),
    #"Changed Type3" = Table.TransformColumnTypes(#"Split Column by Delimiter1",{{"MinCompanySize.1", type text}, {"MinCompanySize.2", type text}}),
    #"Split Column by Delimiter2" = Table.SplitColumn(#"Changed Type3", "MaxCompanySize", Splitter.SplitTextByDelimiter("-", QuoteStyle.Csv), {"MaxCompanySize.1", "MaxCompanySize.2"}),
    #"Changed Type4" = Table.TransformColumnTypes(#"Split Column by Delimiter2",{{"MaxCompanySize.1", type text}, {"MaxCompanySize.2", type text}}),
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type4",{"MinCompanySize.2", "MaxCompanySize.2"}),
    #"Renamed Columns2" = Table.RenameColumns(#"Removed Columns",{{"MinCompanySize.1", "MinCompanySize"}, {"MaxCompanySize.1", "MaxCompanySize"}}),
    #"Filtered Rows" = Table.SelectRows(#"Renamed Columns2", each ([Competitors] <> -1)),
    #"Filtered Rows1" = Table.SelectRows(#"Filtered Rows", each ([Industry] <> -1)),
    #"Filtered Rows2" = Table.SelectRows(#"Filtered Rows1", each ([Revenue] <> "Unknown / Non-Applicable")),
    #"Replaced Value1" = Table.ReplaceValue(#"Filtered Rows2","Rates","",Replacer.ReplaceText,{"Company Name"}),
    #"Removed Columns1" = Table.RemoveColumns(#"Replaced Value1",{"Job Description"})
in
    #"Removed Columns1"
