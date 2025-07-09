FROM alpine:3.22 AS repo

RUN apk update && apk upgrade && apk add --no-cache subversion
WORKDIR /src
RUN svn co -q https://svn.resourcespace.com/svn/rs/releases/10.6 .


FROM python:3.12.11-slim-bookworm

RUN apt-get update && apt-get install -y --no-install-recommends \
	build-essential 

WORKDIR /app

ENV VIRTUAL_ENV=/app/venv
RUN python3 -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

COPY requirements.txt .
COPY --from=repo /src/plugins/faces/scripts/faces_service.py /app/faces_service.py

# makes service listen on all interfaces instead of localhost only
RUN sed -i 's/127\.0\.0\.1/0.0.0.0/g' /app/faces_service.py

RUN pip3 install --upgrade pip && pip3 install --no-cache-dir -r requirements.txt

EXPOSE 8001

CMD ["python3", "faces_service.py"]