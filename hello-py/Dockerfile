FROM python:3-alpine
COPY . ./
RUN pip install -r requirements.txt
EXPOSE 8080
ENTRYPOINT ["python3", "server.py"]