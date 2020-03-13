ARG BUILD_FROM
FROM $BUILD_FROM

ENV LANG C.UTF-8

ENV OCTOPRINT_VERSION=1.3.12
# To have Python packages persistently installed.
ENV PYTHONPATH=/data/python
ENV PATH=${PATH}:/data/python/bin
ENV PYTHONUSERBASE=/data/python

# Change to tmp folder for all installs, easier to clean
WORKDIR /tmp

# Install requirements for add-on
RUN apk add --no-cache jq supervisor py-pip \
    && \
    # Get code for installation, install.
    wget -qO- https://github.com/foosel/OctoPrint/archive/${OCTOPRINT_VERSION}.tar.gz | tar xz \
    && \
    # Compile and install
    apk add --no-cache --virtual build-dependencies \
    build-base python-dev linux-headers \
    && \
    # Octoprint
    apk add --no-cache avahi-compat-libdns_sd \
    && cd /tmp/OctoPrint-${OCTOPRINT_VERSION} \
    && pip install --upgrade pip --user \
    && pip install -r requirements.txt --user \
    && python setup.py install --user \
    && cp -R /data/python /root/ \
    && \
    # Cleanup
    apk del --no-cache build-dependencies \
    && rm -rf /tmp

# Copy data for add-on
COPY run.sh /
RUN chmod a+x /run.sh
COPY supervisord.conf /etc/supervisord.conf

LABEL \
    io.hass.name="OctoPrint (Slim)" \
    io.hass.description="A web frontend for controlling 3D printers." \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Alexander Ryazanov <alryaz@xavux.com>" \
    org.label-schema.description="A web frontend for controlling 3D printers." \
    org.label-schema.build-date=${BUILD_DATE} \
    org.label-schema.name="OctoPrint (Slim)" \
    org.label-schema.schema-version="1.0" \
    org.label-schema.url="" \
    org.label-schema.usage="https://github.com/alryaz/addon-octoprint/tree/master/README.md" \
    org.label-schema.vcs-ref=${BUILD_REF} \
    org.label-schema.vcs-url="https://github.com/alryaz/addon-octoprint" \
    org.label-schema.vendor="Home Assistant Community Add-ons"

EXPOSE 5000
WORKDIR /
CMD [ "/run.sh" ]
