# Query your databases using R or Shiny apps

An essential part of making research and analytics reproducible is automating data query and update. 

I'm going to go over three ways I connect R to our databases to automate data query. 

Each query format serves it's own purpose and I often use multiple in an interactive app. 
1. Simple queries. 
Use this format if your SQL statement is short, runs quickly, and only needs to be executed once. 

```r
terms<- dbGetQuery(db_con, "SELECT term_code, term_name, term_start_date
                   FROM terms
                   WHERE (registration_start_date < sysdate
                   OR term_code LIKE '%50')
                   AND (term_end_date+30) > sysdate")
```
2. Dynamic queries

This is recommended if your query is slow, and you speed it up by re-querying your data based on user-input in an interactive Shiny App. Note that this code will need to be executed inside of a reactive expression in the server section of your shiny app.  

```r
 query.string <- paste0("SELECT fact.DAYS_TO_FULL_TERM_START
             ,TO_DATE(dim_date_key, 'YYYYMMDD') as fecha
             ,fact.term_code
             ,course.college
             ,course.department
             ,section.campus
             ,section.budget_related_ind
             ,section.course_Subject
             ,section.course_number
             ,sum(FTE) as FTE
           FROM WSRPMGR.agg_weekly_enrollments_section fact
           JOIN wsrpmgr.dim_section section
             on fact.dim_section_key = section.dim_section_key
           JOIN wsrpmgr.dim_course course
             on fact.dim_course_key = course.dim_course_key
           WHERE (fact.term_code =",  input$sectionListTerm, " 
                  OR fact.term_code =", input$sectionListTerm, "- 100
                  OR fact.term_code =", input$sectionListTerm, "- 200)
           AND fact.DAYS_TO_FULL_TERM_START > -40
           GROUP BY fact.DAYS_TO_FULL_TERM_START
           ,TO_DATE(dim_date_key, 'YYYYMMDD')
           ,fact.term_code
           ,fact.term_code
           ,course.college
           ,course.department
           ,section.campus
           ,section.budget_related_ind
           ,section.course_Subject
           ,section.course_number")
results<-dbGetQuery(db_con, query.string)
```

3. Complex queries

I use this format type when I'm initially writing a query (therefore doing a whole bunch of exploration) and don't yet know if I need to optimize the query speed. For these I can have my SQL query tool open, executing the query and making changes, while also periodically running this code in R. Read in the file using the `read_file()` function from the `readr` package. 

The main downside of this method is that you cannot comment out lines in your .sql file. 

```r
sections<- dbGetQuery(db_con, read_file("section_query.sql"))
```
