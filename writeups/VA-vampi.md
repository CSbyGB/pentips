# Vulnerable APIs - Vampi

## General info, Get it and deploy it

- [Official repo](https://github.com/erev0s/VAmPI)

### Infos

#### List of Vulnerabilities

- SQLi Injection
- Unauthorized Password Change
- Broken Object Level Authorization
- Mass Assignment
- Excessive Data Exposure through debug endpoint
- User and Password Enumeration
- RegexDOS (Denial of Service)
- Lack of Resources & Rate Limiting

### Deploy it

To deploy it I used Docker

- `docker run -p 5000:5000 erev0s/vampi:latest`  

![Deployed](../.res/2024-01-08-07-47-09.png)  

#### Use Swagger editor

- Go [here](https://editor.swagger.io/)
- Get the yaml in your local machine from [here](https://github.com/erev0s/VAmPI/blob/master/openapi_specs/openapi3.yml) or `wget https://raw.githubusercontent.com/erev0s/VAmPI/master/openapi_specs/openapi3.yml`
- Then you can play in the browser with the request and you get a full list of requests  
![swagger editor](../.res/2024-01-08-08-00-16.png)  

## Hack it

- Launch burp or zap (I will be using burp)

If we access http://127.0.0.1:5000/ we get this page  

![Home page](../.res/2024-01-08-07-51-04.png)  

# COMING SOON