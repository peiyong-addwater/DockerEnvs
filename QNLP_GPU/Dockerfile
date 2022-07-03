FROM ubuntu:20.04

WORKDIR /root/

# Add libcuda dummy dependency
ADD control .
RUN apt-get update && \
	DEBIAN_FRONTEND=noninteractive apt-get install --yes equivs && \
	equivs-build control && \
	dpkg -i libcuda1-dummy_11.1_all.deb && \
	rm control libcuda1-dummy_11.1_all.deb && \
	apt-get remove --yes --purge --autoremove equivs && \
	rm -rf /var/lib/apt/lists/*

# Setup Lambda repository
ADD lambda.gpg .
RUN apt-get update && \
	apt-get install --yes gnupg && \
	apt-key add lambda.gpg && \
	rm lambda.gpg && \
	echo "deb http://archive.lambdalabs.com/ubuntu focal main" > /etc/apt/sources.list.d/lambda.list && \
	echo "Package: *" > /etc/apt/preferences.d/lambda && \
	echo "Pin: origin archive.lambdalabs.com" >> /etc/apt/preferences.d/lambda && \
	echo "Pin-Priority: 1001" >> /etc/apt/preferences.d/lambda && \
	echo "cudnn cudnn/license_preseed select ACCEPT" | debconf-set-selections && \
	apt-get update && \
	DEBIAN_FRONTEND=noninteractive \
		apt-get install \
		--yes \
		--no-install-recommends \
		--option "Acquire::http::No-Cache=true" \
		--option "Acquire::http::Pipeline-Depth=0" \
		lambda-stack-cuda \
		lambda-server && \
	rm -rf /var/lib/apt/lists/*
RUN pip install --upgrade "jax[cuda]" -f https://storage.googleapis.com/jax-releases/jax_cuda_releases.html
RUN pip install -U pip pip-tools && pip install --no-cache-dir cython
RUN pip install -U pip pip-tools && pip install --no-cache-dir numpy
RUN pip install --ignore-installed pyzmq
COPY ./requirements.txt /requirements.txt
RUN pip install -U pip pip-tools && pip install --no-cache-dir --ignore-installed -r /requirements.txt && rm -rf /requirements.txt
RUN pip install -U allennlp
RUN pip install --upgrade google-cloud-storage
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
        biber \
        latexmk\
        texlive-latex-extra && \
        rm -rf /var/lib/apt/lists/*

ENV JUPYTER_ENABLE_LAB=yes
# Setup for nvidia-docker
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
ENV NVIDIA_REQUIRE_CUDA "cuda>=10.2"