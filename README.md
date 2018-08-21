# Query your databases using R or Shiny apps

An essential part of making research and analytics reproducible is automating data query and update. 

I'm going to go over three ways I connect R to our databases to automate data query. 

Each query format serves it's own purpose and I often use multiple in an interactive app. 
1. Simple queries

Use this format if your SQL statement is short, runs quickly, and only needs to be executed once. 

2. Dynamic queries

This is recommended if your query is slow, and you speed it up by re-querying your data based on user-input in an interactive Shiny App. 

3. Complex queries

I use this format type when I'm initially writing a query and don't yet know if I need to optimize the query speed (switching back to method #2).