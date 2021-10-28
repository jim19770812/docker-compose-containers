FROM bestwu/wine:alpine
LABEL maintainer='Peter Wu <piterwu@outlook.com>'

ENV APP=sap \
    GID=1000 \
    UID=1000 \
    EVAL=true

RUN apk add --no-cache p7zip && \
	rm -fr /var/cache/apk/* && \
    addgroup -g $GID sap && \
    adduser -D -h "/home/sap" -u $UID -G sap sap && \
    mkdir /SAPFiles && \
    chown -R sap:sap /SAPFiles && \
    ln -s "/SAPFiles" "/home/sap/SAPFiles"

COPY sap.7z /home/sap/
COPY pdflm16.dll /home/sap/

WORKDIR "/SAPFiles"
VOLUME ["/SAPFiles"]

ADD entrypoint.sh /
RUN chmod +x /entrypoint.sh
USER sap
ENTRYPOINT ["/entrypoint.sh"]
