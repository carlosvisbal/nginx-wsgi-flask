FROM python:3.10

# ARG UID

# upgrade pip
RUN pip install --upgrade pip

# get curl for healthchecks
# RUN apk add curl

# SAP pyRFC
RUN mkdir -p /etc/ld.so.conf.d/ \
 && mkdir -p /usr/sap/nwrfcsdk \
 && echo "# include nwrfcsdk" > /etc/ld.so.conf.d/nwrfcsdk.conf \
 && echo "/usr/sap/nwrfcsdk/lib" >> /etc/ld.so.conf.d/nwrfcsdk.conf

COPY ./nwrfcsdk /usr/sap/nwrfcsdk

ENV SAPNWRFC_HOME=/usr/sap/nwrfcsdk
COPY ./pyrfc-2.5.0-cp310-cp310-linux_x86_64.whl .
RUN pip install --no-cache-dir --upgrade pip
RUN ldconfig
# permissions and nonroot user for tightened security
# RUN adduser -D nonroot
# RUN mkdir /app/ && chown -R nonroot:nonroot /app
RUN mkdir -p /var/log/flask-app && touch /var/log/flask-app/flask-app.err.log && touch /var/log/flask-app/flask-app.out.log
# RUN chown -R nonroot:nonroot /var/log/flask-app
WORKDIR /app
# USER nonroot
RUN useradd -ms /bin/bash -u "1000" appuser && chown -R appuser /app
USER appuser
# copy all the files to the container
COPY --chown=appuser:appuser . .

# venv
ENV VIRTUAL_ENV=/app/venv

# python setup
RUN python -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"
RUN pip install --no-cache-dir pyrfc-2.5.0-cp310-cp310-linux_x86_64.whl
RUN rm pyrfc-2.5.0-cp310-cp310-linux_x86_64.whl

RUN export FLASK_APP=app.py

# Install pip requirements
COPY requirements.txt .
RUN python -m pip install -r requirements.txt

# define the port number the container should expose
EXPOSE 5000

CMD ["python", "app.py"]