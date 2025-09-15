ARG BUILD_FROM
FROM $BUILD_FROM AS NO_VNC

RUN apt-get update && apt-get -y --no-install-recommends install \
    git=1:2.39.5-0+deb12u2

RUN git config --global advice.detachedHead false && \
    git clone https://github.com/novnc/noVNC.git -b v1.5.0 /noVNC

FROM $BUILD_FROM AS WEBSOCKIFY

RUN apt-get update && apt-get -y --no-install-recommends install \
    git=1:2.39.5-0+deb12u2 \
    python3=3.11.2-1+b1 \
    python3-setuptools=66.1.1-1+deb12u2 \
    python3-numpy=1:1.24.2-1+deb12u1 \
    python3-pip=23.0.1+dfsg-1

RUN pip3 install --break-system-packages --no-cache-dir Cython==0.29.32

# hadolint ignore=DL3003
RUN git config --global advice.detachedHead false && \
    git clone https://github.com/novnc/websockify -b v0.10.0 /websockify && \
    cd /websockify && python3 setup.py install

FROM $BUILD_FROM AS RUNNING

RUN apt-get update && apt-get -y --no-install-recommends install \
    git=1:2.39.5-0+deb12u2 \
    python3=3.11.2-1+b1 \
    python3-setuptools=66.1.1-1+deb12u2 \
    python3-numpy=1:1.24.2-1+deb12u1 \
    procps=2:4.0.2-3  \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY --from=NO_VNC /noVNC /noVNC
COPY --from=WEBSOCKIFY /usr/local/lib/python3.11/dist-packages/ /usr/local/lib/python3.11/dist-packages/
COPY --from=WEBSOCKIFY /usr/local/bin/websockify /usr/local/bin/websockify

ENTRYPOINT [ "/init" ]
CMD []
COPY root /
