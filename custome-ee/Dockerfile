cat Dockerfile
FROM quay.io/ansible/awx-ee:latest
USER root
RUN mkdir -p /.ansible/tmp
RUN ansible-galaxy collection install community.windows
#RUN ansible-galaxy collection install ansible.builtin
#RUN ansible-galaxy collection install ansible.netcommmon
RUN ansible-galaxy collection install ansible.posix
RUN ansible-galaxy collection install ansible.utils
RUN ansible-galaxy collection install ansible.windows
RUN ansible-galaxy collection install awx.awx
#RUN ansible-galaxy collection install cloud.commmon
RUN pip install pywinrm
RUN pip install pywinrm[credssp]
USER 1000
