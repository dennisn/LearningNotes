# Google AI - For Data Analysis
  - Course: https://www.coursera.org/learn/google-ai-for-data-analysis/home/module/1

  - Human roles:
    + Ask the questions
    + Understand the implications of the result
    + Verify findings
    + Make decision

## Define key performance metrics for Reports
  - Identify key presentation metrics
    ```[Context: company, meeting type, goals]. What are the top 5 metrics I should consider for my presentation?```
  - Anticipate leadership questions
    ```What questions might our leadership team ask about [one of Gemini's suggested metrics from the previous step]?```

## Turn screenshot into data insights
"Google Sheets" can be used to extract structured data from image

  - Extract & format data from image from Gemini --> export to Google Sheets
    ```Extract the [data, e.g. reviews] from this [source, e.g. screenshot] and turn them into a [format, e.g. table] with the following columns [column headers, e.g. date, text, and # of stars].```
  - Annotate your data: for each new column, use `=AI()` functions, taking in a prompt & cell as input
    ```=AI("Analyze the [classify-data, e.g. customer feedback] and classify it into [categories, e.g. Positive, Neutral, or Negative]", [cell with customer feedback text, e.g. B2])```

## Gain data insights using Google Sheets
  - Get a high-level data summary
  - Ask a question about the data
  - Use the data to build a narrative: `why might the [specific finding] ?` --> general reason for specific finding
  - Create data visualization