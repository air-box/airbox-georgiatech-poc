# AirBox Key Gateway Demo

AirBox Key Gateway is needed if using closed hardware / software (e.g., ADCs, load balancers that use properietary SSL implementations etc.) that currently handles your machine identities (certificates, private keys, etc.). In those cases, there is an option of deploying AirBox Key Gateway to allow operation without having to deal with mandatory keys in those hardware / software systems.

AirBox Key Gateway is built using industry standard high performance Nginx deployed in reverse proxy mode. In this mode, it sits in front of the closed harware / software solution and enables you to benefit from keyless operations that AirBox enables.

## Settting up demo (RHEL 8)

1. Clone this repo and go to folder
```git clone https://github.com/air-box/airbox-georgiatech-poc.git && airbox-georgiatech-poc```

2. Setup nginx
	- Install
	```dnf install nginx```
	- Copy demo configuration
	```
	cp keygateway.conf /etc/nginx/conf.d/ 
	cp nginx.conf /etc/nginx/nginx.conf
	```
3. Create air-box directory
```mkdir -p /opt/air-box```

3. Populate appropriate files for demo

	- Install dummy certs & keys needed for demo
	```
	cp dhparam.pem /opt/air-box/     
	cp nginx-selfsigned.crt /opt/air-box/
	cp nginx-selfsigned.key /opt/air-box/
	```

	- Install keyvisor
	```
	cp keyvisor.so /opt/air-box
	cp keyvisor.conf /opt/air-box/
	cp keyless /opt/air-box
	``` 
4. Run AirBox Key Gateway
``` sudo ./keyless nginx```
