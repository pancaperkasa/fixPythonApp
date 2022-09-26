FROM  python:3.6

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

RUN mkdir /app
WORKDIR /app
COPY src/requirements.txt /app/requirements.txt
RUN pip install -r requirements.txt
COPY src/ /app

#Trivy
RUN mkdir /trivy
WORKDIR /trivy
COPY deploy/html.tpl /trivy
RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin
RUN trivy --format template --template "@html.tpl" -o reportrootfs.html --severity HIGH,CRITICAL rootfs /
#Trivy


EXPOSE 10007
CMD ["python", "routes.py"]