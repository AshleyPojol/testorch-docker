# testorch-docker
running Jmeter, with InfluxDB 2.0 with Grafana Integration

1. git clone "https://github.com/AshleyPojol/testorch-docker.git"
2. then, docker compose up -d
3. go to "http://localhost:8086/" in your browser
 ![image](https://github.com/user-attachments/assets/4b6611ab-0d72-435d-9434-b7ff82293fd9)
then login with your credentials.
<br> Username: admin <br>
Password: admin123
4. Go to "Load Data" > "Buckets" > "Create Bucket" named jmeter, then click "Create".
![image](https://github.com/user-attachments/assets/06be62be-c204-4138-9b21-357c0cdd4782)

5. Go to "Load Data" > "Tokens" > "Generate Token", "Read/Write Token" then put in the description "jmeter", then click "Save". also select the bucket "jmeter" for the read and write. <br>
![image](https://github.com/user-attachments/assets/4688c159-ef4c-4375-8495-e4e29827bf32) <br>

