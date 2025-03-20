FROM python:3.9
WORKDIR /app
COPY app.py /app/
RUN pip install flask
ENV FLASK_ENV=production
CMD ["python", "app.py"]