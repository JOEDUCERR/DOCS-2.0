claude auth status --text : Checking if anthropic key is present or not

* Pulled the project demo files

* made env.local using .env.example
* gave it 600 permission

* cp .env.example .env.local

* ran it using:
* npm run dev -- --hostname 127.0.0.1 --port 3100

* tested it using:
* curl -I http://127.0.0.1:3100

* To actually see the result: (.pem key permission should be 600)
* ssh -i /home/joeducer/Downloads/recruitment-key.pem -L 3100:127.0.0.1:3100 ubuntu@13.57.51.242