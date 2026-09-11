ARG BUILD_FROM
FROM ${BUILD_FROM}

# The binary is prebuilt per architecture; nothing is compiled here.
ARG BUILD_ARCH
COPY bin/ava_${BUILD_ARCH} /usr/bin/ava
COPY run.sh /run.sh
RUN chmod +x /usr/bin/ava /run.sh

CMD ["/run.sh"]
