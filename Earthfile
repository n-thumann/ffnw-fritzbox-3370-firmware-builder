VERSION 0.7
FROM debian:bullseye

dependencies:
    RUN apt-get update
    RUN apt-get install -y --no-install-recommends \
        git \
        subversion \
        python3 \
        build-essential \
        gawk \
        unzip \
        libncurses-dev \
        libz-dev \
        libssl-dev \
        wget \
        time \
        rsync \
        ca-certificates \
        file
    RUN adduser builder
    USER builder
    WORKDIR /home/builder/
    RUN git config --global user.name "Your Name"
    RUN git config --global user.email "you@example.com"
    RUN git config --global init.defaultBranch main

setup-gluon:
    FROM +dependencies
    ARG --required gluon_release
    ARG --required gluon_commit
    # Cannot use GIT CLONE here, because it creates a shallow clone and
    # fetching other commits defaults to SSH instead of HTTPS
    RUN git clone https://github.com/freifunk-gluon/gluon.git --single-branch --branch $gluon_release gluon
    WORKDIR /home/builder/gluon
    RUN git checkout $gluon_commit

apply-setup-mode-patch:
    FROM +setup-gluon
    RUN wget https://github.com/dzzinstant/gluon/commit/11a235876060c06c088794b2bf4e0f6d89f1e5a8.patch
    RUN git am 11a235876060c06c088794b2bf4e0f6d89f1e5a8.patch

setup-ffnw-site:
    FROM +apply-setup-mode-patch
    ARG --required ffnw_tag
    ARG --required ffnw_release
    GIT CLONE  --branch $ffnw_tag https://git.ffnw.de/ffnw-firmware/siteconf.git site
    WORKDIR /home/builder/gluon/site
    # Fix tags to get rid off "rc/" prefix. Building will fail otherwise
    # (see "getversion.sh" in https://github.com/freifunk-gluon/gluon/blob/670da836c7c75769dd7abbf87fde66b713cdf2a1/docs/dev/build.rst)
    RUN git tag -d $ffnw_tag
    RUN git tag $ffnw_release
    RUN ./buildscript.sh patch
    RUN ./buildscript.sh prepare GLUON_AUTOUPDATER_BRANCH stable
    RUN ./buildscript.sh prepare GLUON_RELEASE $ffnw_release
    RUN ./buildscript.sh prepare l2tp

build:
    FROM +setup-ffnw-site
    WORKDIR /home/builder/gluon
    RUN make -j $(nproc) \
        V=s \
        GLUON_TARGET=lantiq-xrx200 \
        GLUON_DEVICES=avm-fritz-box-3370-rev-2-micron-nand \
        GLUON_AUTOUPDATER_ENABLED=0 \
        BROKEN=1
    SAVE ARTIFACT output/images/* AS LOCAL ./images/
