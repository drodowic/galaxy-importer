FROM quay.io/ansible/base-test-container:1.1.0

COPY entrypoint.sh /entrypoint

RUN useradd user1 \
      --uid 1000 \
      --no-create-home \
      --gid root && \
    apt-get update -y && \
    apt-get install -y wget && \
    chmod +x /entrypoint && \
    mkdir -m 0775 /archive && \
    mkdir -p -m 0775 /ansible_collections /ansible_collections/ns /ansible_collections/ns/col /home/user1 && \
    touch /ansible_collections/ns/col/placeholder.txt && \
    chown -R user1:root /ansible_collections /home/user1 && \
    # Sets up python2 env since running from base-test-container not default-test-container
    python2.6 -m pip.__main__ install virtualenv==15.2.0 --index https://d2c8fqinjk13kw.cloudfront.net/simple/ --disable-pip-version-check && \
    python2.7 -m pip install virtualenv==16.7.12 --disable-pip-version-check && \
    # On updating ansible-core version, update the FROM statement to the matching base-test-container version
    # After quay image is built, tag should be updated in ansible_test/job_template.yaml
    python3.9 -m pip install ansible-core==2.12.0 --disable-pip-version-check && \
    # The following updates dir and permissions for where ansible-test sanity writes files
    # NOTE: when container non-privileged, if sanity test writes to new dir, it will fail
    touch /usr/local/lib/python3.9/dist-packages/ansible.egg-info && \
    chmod 0775 /usr/local/lib/python3.9/dist-packages/ansible.egg-info && \
    mkdir -m 0775 /.ansible /.pylint.d

ENV HOME /
USER 1000

RUN cd /ansible_collections/ns/col && \
    ansible-test sanity --prime-venvs

ENTRYPOINT ["/entrypoint"]
