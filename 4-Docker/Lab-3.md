## Lab-3

### Create a flask app and run this app into a docker container. (Flast is a micro framework of python).
Steps:
- Create a directory `flask-app` using `mkdir flsk-app`.
- Go to this dir using ` cd flask-app`.
- Create a flask-app using below code and save it as `app.py`.
```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, Dosto!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000,debug=True)
```

- Create `Dockerfile` with bekow code and save it in the same dir.
```
#Base Image with python
FROM python:3.9

#Working directory for the app
WORKDIR app/

#Copy code into the current dir
COPY app.py .

#install required libraries
RUN pip install Flask

#Run the application
CMD ["python","app.py"]
```

- build an image using above docker file.
```
docker build . -t flask-app
```
- Chaeck images
```
docler images
```
- Now run image in detach mode on 5000 host and 5000 container port.
```
docker run -d -p 5000:5000 flask-app:latest
```
- Try to access from browser using http://ip:5000
- In case unable to access then open 5000 port over AWS portal.



