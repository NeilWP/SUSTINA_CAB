
Albert   

The Datamodel is almost ready - time to get reviewing it together.
I would suggest that we get a bit of a factory going - where when the need to explain a  
* CHANGE or  
* an ERROR remediation 
is identified  we add a new table in the specific page to explain what the issue / change is setting the status to open  OPEN 
Keeo it on the main branch - we don't need to make this one open to lots of changesand merges .

I have a script which trawls thruigh the dcatalogue and creaets the underlying data base objects 

# CHANGE DEMAND

This section records all change requests, defects, and remediation activities related to this ERD entity.

| Date Raised | Change / Error | Description of Request / Issue | Raised By | Status | Date Resolved |
|-------------|----------------|--------------------------------|-----------|--------|----------------|
| YYYY-MM-DD  | Change / Error | Describe the required update, defect, or modelling issue | Name | Open / In Progress / Closed | YYYY-MM-DD |
|             |                |                                |           |        |                |

---

## Ho do clients get the data into the data model?
I see three ways :
* MANUAL - KEY IN - slow
* MANUAL - File uploads csv, text - equally slow. 
* HANDS OFF - JSON/XBRL/XML - slick.
All three mechanisms  will float to the  device  to allow data to  flwo into the database.
--- 
