# Introduction #

To determine pregnancy status, we currently have a very complex algorithm that we use to determine the status of a patient at any given point in time.  The attached file contains a description of the steps involved.  The actual code is in the file 'backend/sharedRptFunctions.php' and includes the functions:

  * fillLmpTable
  * fillPregnancyTable
  * comparePregnancyTables


# Algorithm #

**GOAL: Determining pregnancy status between the dates `<start>` and `<end>`**


Step 1: Only non-male patients (patients with 'F' or nothing checked for the 'Gender' question on registration form).

Step 2: Look at HIV intake & f/u forms, and store patient and the most recent visit date (earlier than `<end>`, but not more than 10 months earlier) where the answer to the "Currently pregnant" question is 'Yes'.

Step 3: Look at lab forms, and store patient and the most recent result date (earlier than `<end>`, but not more than 10 months earlier) where the pregnancy test result is 'Pos'. If a more recent date for a patient stored in the previous steps is found, store the more recent date for that patient.

Step 4: Look at DPA (probable delivery date) dates on ob/gyn forms, and store patient and the most recent visit date (earlier than `<end>`) where there is a date in the DPA field between `<start>` and `<end>` plus nine months. If a more recent date for a patient stored in the previous steps is found, store the more recent date for that patient.

Step 5: Look at labor & delivery forms, and store patient and the most recent visit date (earlier than `<end>`) where the visit date is between `<start>` and `<end>`. If a more recent date for a patient stored in the previous steps is found, store the more recent date for that patient.

Step 6: Look at primary care & ob/gyn forms, and store patient and the most recent visit date (earlier than `<end>`, but not more than 10 months earlier) where the pregnancy diagnosis is checked on primary care forms, or any of the following are checked on ob/gyn forms: Grossesse intra uterine, Diabete+Grossess, HTA+Grossess, Eclampsie, Pre-eclampsie, Hyperemese Gravidique, Hemorragie troisieme trismestre, Menace d'accouchement prematuree, Rupture prematuree des membranes.  If a more recent date for a patient stored in the previous steps is found, store the more recent date for that patient.

Step 7: Look at HIV intake & f/u forms, and remove any patient where the answer to the "Currently pregnant" question is 'No' and the visit date is more recent than the date stored for the patient in the previous steps (but visit date is earlier than `<end>`, but not more than 10 months earlier).

Step 8: Look at lab forms, and remove any patient where the pregnancy test result is 'Neg' and the result date is more recent than the date stored for the patient in the previous steps (but visit date is earlier than `<end>`, but not more than 10 months earlier).

Step 9: Look at HIV intake & f/u, and ob/gyn forms, and remove any patient where the LMP/DDR (date of last period) is more recent than the date stored for the patient in the previous steps (but LMP/DDR date is earlier than `<start>`).

Step 10: Look at labor & delivery forms, and remove any patient where the visit date is more recent than the date stored for the patient in the previous steps (but visit date is earlier than `<start>`).

> That should leave a list of all patients who, by the most recent data available in the system, are assumed to be pregnant between `<start>` and `<end>`.