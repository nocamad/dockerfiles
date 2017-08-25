# Dockerfiles for:

- oscript (gitsync)

build: docker build -t name/gitsync .

run: docker run -v path_to_conf1c:/opt/1C/v8.3/x86_64/conf -v path_to_logs1c:/var/log/1c/logs -v path_to_repsgit:/opt/data/repsgit -v path_to_temps:/opt/data/temps -v path_to_repos:/opt/data/repos -d -i -t -p port:22 --privileged=true --restart=always --name gitsync name/gitsync

run gitsync in conteiner: oscript /usr/share/oscript/lib/gitsync/src/gitsync.os /opt/data/temps/rep http://path_to_git_server/../name_rep.git /opt/data/repsgit/name_rep/src/cf -v8version version_1c -debug on -format hierarchical -tempdir /opt/data/temps/temp -userRep user_rep -passRep pass_user_rep

