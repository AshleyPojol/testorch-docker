![image](https://github.com/user-attachments/assets/7c4f0870-5cef-46b1-a510-23c1bd13b0b5)![image](https://github.com/user-attachments/assets/ca3ebebf-f837-4b89-aa5f-c28a938b1891)# testorch-docker
running Jmeter, with InfluxDB 2.0 with Grafana Integration

1. git clone "https://github.com/AshleyPojol/testorch-docker.git"
2. then, docker compose up -d
3. go to "http://localhost:8086/" in your browser
 ![image](https://github.com/user-attachments/assets/4b6611ab-0d72-435d-9434-b7ff82293fd9)
then <br>
    Enter the following credentials: <br>
    <ul>
     <li>Email or Username: admin</li>
     <li>Password: admin123 </li>
    </ul>
4. Go to "Load Data" > "Buckets" > "Create Bucket" named jmeter, then click "Create".
![image](https://github.com/user-attachments/assets/06be62be-c204-4138-9b21-357c0cdd4782)

5. Go to "Load Data" > "Tokens" > "Generate Token", "Read/Write Token" then put in the description "jmeter", then click "Save". also select the bucket "jmeter" for the read and write. <br>
![image](https://github.com/user-attachments/assets/4688c159-ef4c-4375-8495-e4e29827bf32) <br>

6. Go to the extracted files of Jmeter, Click "apache-jmeter-5.6.3" > "bin" > then click "jmeter.bat" it should show the Apache Jmeter.
![image](https://github.com/user-attachments/assets/2bdc316f-1f55-4f54-b937-a33becf55049) <br>

7. Right-Click on the "Test Plan", then click "Threads (User)", then "Thread Groups".
![image](https://github.com/user-attachments/assets/d512288e-fbee-4270-89a6-5b100bf51c67) <br>

8. Then, right-cick on the "Test Plan", click "Listener" and then "Backend Listener".
![image](https://github.com/user-attachments/assets/bc3603dc-0cd2-4c96-bcef-bad3cf4db10a) <br>

9. Go to your "Backend Listener" and set the Backend Listener Implementation to "org.apache.jmeter.visualizers.backend.influxdb.InfluxdbBackendListenerClient".
![image](https://github.com/user-attachments/assets/9547c6b4-a2c1-4bec-a9c6-43e4cb9c32db) <br>

10. Edit the following on the Parameters
    <ul>
     <li>influxdbUrl: http://localhost:8086/api/v2/write?org=testorch$bucket=jmeter</li>
     <li>application: jmeter</li>
     <li>influxdbToken: (you can get it in InfluxDB > Token > jmeter > copy the token.)</li>
     <li>influxdbBukcet: jmeter</li>
     <li>influxdbOrganization: testorch</li>
    </ul>
![image](https://github.com/user-attachments/assets/63a9d41d-7676-4bd9-b7d6-b53380b8e615) <br>
then run the it by pressing "Ctrl+R" or the Play button above. 

11. Go to InfluxDB > "Data Explorer" , Click on "jmeter", then you can choose from the filter the sample data being sent from Jmeter. after you check your data click on "Submit" then it will show the data. 
![image](https://github.com/user-attachments/assets/26e12d01-f458-4b66-aa5b-8e38ddc725fd) <br>
You can toggle the different visually graph from the dropdown above, in the example I choose the "Histogram".

12. then click on the "Save As" after toggling with the Data in your Data Explorer then click "Create a New Dashboard." 
![image](https://github.com/user-attachments/assets/f1d86056-ba67-4cd8-8bc7-71dd8cde4e9c) <br>
![image](https://github.com/user-attachments/assets/0aa2e569-db6a-4ee8-97f0-665d57b7e97e) <br>
then it will direct you the newly created dashboard. 
![image](https://github.com/user-attachments/assets/e0ec37c0-1535-4fdb-85be-abce2e622146).

13. After creating the dashboard for InfluxDB, let's proceed to Grafana starting with going to Grafana in http://localhost:8087. <br>
    Enter the following credentials: <br>
    <ul>
     <li>Email or Username: admin</li>
     <li>Password: admin123 </li>
    </ul>
![image](https://github.com/user-attachments/assets/16cea4ee-ffaf-4ab9-ba18-644c5b81259e) <br> 

14. Go to "Data Source" which is located on the sidebar. <br>
![image](https://github.com/user-attachments/assets/d96628cf-2fb8-43e6-990b-39dd6a236123)

15. Click on "Add New Data Source" then look for "InfluxDB".
    
16. After clicking on the InfluxDB as your data source change the following: <br>
<ul>
 <ol> Query Language: Flux</ol>
 <ol> URL: http://influxdb:8086</ol>
 <ol> Organization: testorch</ol>
 <ol> Token: (you can get it in InfluxDB > Token > jmeter > copy the token.) </ol>
 <ol> Default Bucket: jmeter </ol>
</ul>

![image](https://github.com/user-attachments/assets/f3c0ff26-a3f0-4475-8360-988cfb919489) <br>
click on "Save & Test".

17. then go to "Dashboards" located at the sidebar.
    
18. Click on "New" and "New Dashboard" > "Add Visualization" > select "InfluxDB" as your data source.
    
19. Go back to InfluxDB on your dashboard click on the "Settings" > "Configure".
![image](https://github.com/user-attachments/assets/4d9022ee-0d2f-40ee-9ace-eb6997612604)

20. Click on Script Editor, then copy the whole query.
![image](https://github.com/user-attachments/assets/ad3fea4f-62d0-49bb-8799-7fe10d359d4e) <br>
![image](https://github.com/user-attachments/assets/14892daf-c820-4740-8986-efdeb50f2922) <br>

In my example my query is <br>
from(bucket: "jmeter") <br>
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop) <br>
  |> filter(fn: (r) => r["result"] == "success") <br>
  |> filter(fn: (r) => r["_measurement"] == "qc_requests_total") <br>
  |> filter(fn: (r) => r["_field"] == "counter") <br>
  |> filter(fn: (r) => r["org"] == "c6213d9ee004312a") <br>
  |> aggregateWindow(every: v.windowPeriod, fn: last, createEmpty: false) <br>
  |> yield(name: "last") <br>

21. Paste the query on Grafana. <br>
![image](https://github.com/user-attachments/assets/485ee273-190d-4992-9db2-67e617da4b6d) <br>

22. then click on "Query Inspector" > "Refresh" > then it will show the data on the dashboard. 
![image](https://github.com/user-attachments/assets/a654e264-d430-48d3-bad3-6a29a4566177) <br>
You can also toggle on which graphical representation you are going to use as for my example I used "Time Series." <br>
![image](https://github.com/user-attachments/assets/e148ce10-c368-438f-9b23-577157d0ab46) <br>
![image](https://github.com/user-attachments/assets/484a75f6-347c-4f8c-b4c4-197d46e62f32)


23. Click on "Apply", then you've already successfully shown the data from InfluxDB to Grafana. 
![image](https://github.com/user-attachments/assets/ecb46098-857e-4d71-907d-191e32ad266f)



# Congratulations, You have successfully deployed InfluxDB, Jmeter & Grafana on Docker and also connected Jmeter as a Data Source to InfluxDB, then connected InfluxDB to show the data via dashboard in Grafana. #

## Don't forget to star "⭐" this tutorial ##










