FROM python:3-alpine
COPY . ./
RUN pip install -r requirements.txt
EXPOSE 5000
ENTRYPOINT ["python3", "server.py"]