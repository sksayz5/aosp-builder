############################################################
# base on Ubuntu
# custom_env: build_arg='repo_device=https://github.com/LineageOS/android_device_xxx'
############################################################

ARG base_image=ubuntu:devel
FROM ${base_image}

ARG build_arg="" \
    flag_split=","
RUN set -x \
    # ===== Set environment variable =====
    && export build_arg=$(echo "${build_arg}" | sed 's|^[\"]*||;s|[\"]*$||' | sed "s|^[\']*||;s|[\']*$||") \
    && echo "${build_arg}" \
    && echo "${build_arg}" | tr "${flag_split}" '\n' | while IFS='=' read -r key value; do \
        if [ -n "$key" ] && [ -n "$value" ]; then \
            echo "$key=$value" >> /etc/environment; \
        fi; \
    done \
    && set -a \
    && . /etc/environment \
    && unset build_arg \
    && set +a \
    && printenv \
    # ===== apt dependencies =====
	&& export DEBIAN_FRONTEND=noninteractive \
	&& apt-get update --ignore-missing -y -qq \
    && apt-get install --no-install-recommends --no-install-suggests --fix-missing -y -qq \
		bc \
        bison \
        build-essential \
        ca-certificates \
        curl \
        flex \
        g++ \
        gcc \
        gcc-aarch64-linux-gnu \
        git \
        git-lfs \
        gnupg \
        gperf \
        libelf-dev \
        lz4 \
        libssl-dev \
        libxml2 \
        libxml2-utils \
        lzop \
        make \
        python3 \
        rsync \
        schedtool \
        squashfs-tools \
        vim \
        xsltproc \
        zip \
        zlib1g-dev \
        sudo \
        brotli \
        unzip \
    # ===== List upgradable =====
    && apt list --upgradable \
    # ===== Clean apt =====
    && apt-get autoremove --purge -y -qq \
    && apt-get clean -y -qq \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /var/log/*.log \
	&& unset DEBIAN_FRONTEND \
    # ===== fix /dev/shm =====
    && chmod +x /dev/shm \
    # ===== android =====
    && dir_android="/android" \
    && mkdir -p ${dir_android} \
    # ----- sdat2img.py -----
    && dir_sdat2img="/sdat2img" \
    && mkdir -p ${dir_sdat2img} \
    && git clone "https://github.com/xpirt/sdat2img" ${dir_sdat2img} --single-branch --depth=1 --branch=master \
    # ----- device repository -----
    && dir_device="/device" \
    && mkdir -p ${dir_device} \
    && if [ -n "${repo_device}" ] ; then \
        git clone ${repo_device} ${dir_device} --single-branch --depth=1 \
    fi; \
    # ===== docker-entrypoint.sh =====
    && sh_entrypoint="/docker-entrypoint.sh" \
    && echo "#!/usr/bin/env bash" >> ${sh_entrypoint} \
    && echo "uname -a" >> ${sh_entrypoint} \
	&& echo "sleep infinity" >> ${sh_entrypoint} \
	&& chmod +x ${sh_entrypoint}

WORKDIR "/sdat2img"

ENTRYPOINT ["/docker-entrypoint.sh"]

