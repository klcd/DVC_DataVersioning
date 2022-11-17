FROM python:3.10-slim

#Locale
RUN apt-get update && apt-get install -y locales && rm -rf /var/lib/apt/lists/* \
	&& localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANG en_US.utf8

RUN apt-get update && apt-get install -y git curl vim

#Packages
RUN pip3 install dvc


#Add user
RUN addgroup datascientist && useradd -ms /bin/bash -g datascientist datascientist
RUN adduser datascientist sudo
RUN echo "datascientist:datascientist" | chpasswd


RUN curl -L https://raw.github.com/git/git/master/contrib/completion/git-prompt.sh > /home/datascientist/.bash_git && echo "source /home/datascientist/.bash_git" >> /home/datascientist/.bashrc
RUN sed -Ei 's/(PS1=.*)(\\\[\\033\[00m\\\]\\\$.*)/\1\\[\\033[01;33m\\]$(__git_ps1)\2/p' /home/datascientist/.bashrc
