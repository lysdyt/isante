# Introduction #

The iSanté database schema has evolved through three stages:
  * Initially, the database consisted of a large number of de-normalized tables roughly corresponding to the forms and sections of forms required by the application.
  * The second stage normalized specific concepts (conditions, drugs, symptoms, and labs, etc.) into corresponding tables. each with their own related attributes.
  * The third stage consisted of bringing iSante closer to the openMRS http://openmrs.org/ concept model, which consists basically of observations stored in one large concept-value pairs table.

# Details #

![https://sites.google.com/site/haitioejan2012/home/iSant%C3%A9%20Forms.png](https://sites.google.com/site/haitioejan2012/home/iSant%C3%A9%20Forms.png)