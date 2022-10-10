FROM python:3-alpine
WORKDIR /usr/src/app
EXPOSE 8000
COPY . .
RUN pip install -qr requirements.txt
CMD ["python3", "./server.py"]
