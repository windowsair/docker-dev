Docker-dev: the container engine with golang sources. 
============================
#How to use it?

##1.Building development environment.
    
You can see an example of building a compilation environment in an Ubuntu environment:
```shell
#!/bin/bash
cat >> /etc/profile.d/dockerhack.sh <<EOF
export DOCKER_BUILDTAGS="apparmor selinux"
export DOCKER_CROSSPLATFORMS="linux/386 linux/arm 	darwin/amd64 darwin/386 	freebsd/amd64 freebsd/386 freebsd/arm 	windows/amd64 windows/386"
export DOCKER_PY_COMMIT="47ab89ec2bd3bddf1221b856ffbaff333edeabb4"
export GOARM="5"
export GOPATH="/go:/go/src/github.com/docker/docker/vendor"
export GO_LINT_COMMIT="f42f5c1c440621302702cb0741e9d2ca547ae80f"
export GO_TOOLS_COMMIT="069d2f3bcb68257b627205f0486d6cc69a231ff9"
export GO_VERSION="1.4.3"
export HOME="/root"
export HOSTNAME="553770d7c964"
export LXC_VERSION="1.1.2"
export NOTARY_COMMIT="8e8122eb5528f621afcd4e2854c47302f17392f7"
export OLDPWD="/go/src/github.com/docker"
export PATH="/go/bin:/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
export PWD="/etc/profile.d"
export REGISTRY_COMMIT="ec87e9b6971d831f0eff752ddb54fb64693e51cd"
export RSRC_COMMIT="e48dbf1b7fc464a9e85fcec450dddf80816b76e0"
export SHLVL="1"
export TERM="xterm"
export TOMLV_COMMIT="9baf8a8a9f2ed20a8e54160840c492f937eeaf9a"
EOF
source /etc/profile



export PATH="/go/bin:/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
echo '#!/bin/bash' > /usr/sbin/policy-rc.d && echo 'exit 101' >> /usr/sbin/policy-rc.d && chmod +x /usr/sbin/policy-rc.d && dpkg-divert --local --rename --add /sbin/initctl && cp -a /usr/sbin/policy-rc.d /sbin/initctl && sed -i 's/^exit.*/exit 0/' /sbin/initctl && echo 'force-unsafe-io' > /etc/dpkg/dpkg.cfg.d/docker-apt-speedup && echo 'DPkg::Post-Invoke { "rm -f /var/cache/apt/archives/*.deb /var/cache/apt/archives/partial/*.deb /var/cache/apt/*.bin || true"; };' > /etc/apt/apt.conf.d/docker-clean && echo 'APT::Update::Post-Invoke { "rm -f /var/cache/apt/archives/*.deb /var/cache/apt/archives/partial/*.deb /var/cache/apt/*.bin || true"; };' >> /etc/apt/apt.conf.d/docker-clean && echo 'Dir::Cache::pkgcache ""; Dir::Cache::srcpkgcache "";' >> /etc/apt/apt.conf.d/docker-clean && echo 'Acquire::Languages "none";' > /etc/apt/apt.conf.d/docker-no-languages && echo 'Acquire::GzipIndexes "true"; Acquire::CompressionTypes::Order:: "gz";' > /etc/apt/apt.conf.d/docker-gzip-indexes 
sed -i 's/^#\s*\(deb.*universe\)$/\1/g' /etc/apt/sources.list 
apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys E871F18B51E0147C77796AC81196BA81F6B0FC61 
echo deb http://ppa.launchpad.net/zfs-native/stable/ubuntu trusty main > /etc/apt/sources.list.d/zfs.list 
apt-get update && apt-get install -y apparmor aufs-tools automake bash-completion btrfs-tools build-essential createrepo curl dpkg-sig git iptables libapparmor-dev libcap-dev libsqlite3-dev mercurial parallel python-mock python-pip python-websocket reprepro ruby1.9.1 ruby1.9.1-dev s3cmd=1.1.0* ubuntu-zfs libzfs-dev --no-install-recommends 
git clone -b v2_02_103 http://sourceware.org/git/lvm2.git /usr/local/lvm2 
cd /usr/local/lvm2 && ./configure --enable-static_link && make  && make install
mkdir -p /usr/src/lxc && curl -sSL https://linuxcontainers.org/downloads/lxc/lxc-${LXC_VERSION}.tar.gz | tar -v -C /usr/src/lxc/ -xz --strip-components=1 
cd /usr/src/lxc && ./configure && make && make install && ldconfig 
curl -sSL https://golang.org/dl/go${GO_VERSION}.src.tar.gz | tar -v -C /usr/local -xz && mkdir -p /go/bin 
cd /usr/local/go/src && ./make.bash --no-clean 2>&1 
cd /usr/local/go/src && set -x && for platform in $DOCKER_CROSSPLATFORMS; do GOOS=${platform%/*} GOARCH=${platform##*/} ./make.bash --no-clean 2>&1; done 
git clone https://github.com/golang/tools.git /go/src/golang.org/x/tools && (cd /go/src/golang.org/x/tools && git checkout -q $GO_TOOLS_COMMIT) && go install -v golang.org/x/tools/cmd/cover && go install -v golang.org/x/tools/cmd/vet 
git clone https://github.com/golang/lint.git /go/src/github.com/golang/lint && (cd /go/src/github.com/golang/lint && git checkout -q $GO_LINT_COMMIT) && go install -v github.com/golang/lint/golint 
gem install --no-rdoc --no-ri fpm --version 1.3.2 
set -x && export GOPATH="$(mktemp -d)" && git clone https://github.com/docker/distribution.git "$GOPATH/src/github.com/docker/distribution" && (cd "$GOPATH/src/github.com/docker/distribution" && git checkout -q "$REGISTRY_COMMIT") && GOPATH="$GOPATH/src/github.com/docker/distribution/Godeps/_workspace:$GOPATH" go build -o /usr/local/bin/registry-v2 github.com/docker/distribution/cmd/registry && rm -rf "$GOPATH" 
set -x && export GOPATH="$(mktemp -d)" && git clone https://github.com/docker/notary.git "$GOPATH/src/github.com/docker/notary" && (cd "$GOPATH/src/github.com/docker/notary" && git checkout -q "$NOTARY_COMMIT") && GOPATH="$GOPATH/src/github.com/docker/notary/Godeps/_workspace:$GOPATH" go build -o /usr/local/bin/notary-server github.com/docker/notary/cmd/notary-server && rm -rf "$GOPATH" 
git clone https://github.com/docker/docker-py.git /docker-py && cd /docker-py && git checkout -q $DOCKER_PY_COMMIT 
{ echo '[default]'; echo 'access_key=$AWS_ACCESS_KEY'; echo 'secret_key=$AWS_SECRET_KEY'; } > ~/.s3cfg 
git config --global user.email 'docker-dummy@example.com' 
ln -sfv $PWD/.bashrc ~/.bashrc 
ln -sv $PWD/contrib/completion/bash/docker /etc/bash_completion.d/docker 
/go/src/github.com/docker/docker/contrib/download-frozen-image.sh /docker-frozen-images busybox:latest@8c2e06607696bd4afb3d03b687e361cc43cf8ec1a4a725bc96e39f05ba97dd55 hello-world:frozen@91c95931e552b11604fea91c2f537284149ec32fff0f700a4769cfd31d7696ae jess/unshare@5c9f6ea50341a2a8eb6677527f2bdedbf331ae894a41714fda770fb130f3314d 
set -x && export GOPATH="$(mktemp -d)" && git clone -b v1.0.3 https://github.com/cpuguy83/go-md2man.git "$GOPATH/src/github.com/cpuguy83/go-md2man" && git clone -b v1.2 https://github.com/russross/blackfriday.git "$GOPATH/src/github.com/russross/blackfriday" && go get -v -d github.com/cpuguy83/go-md2man && go build -v -o /usr/local/bin/go-md2man github.com/cpuguy83/go-md2man && rm -rf "$GOPATH" 
set -x && export GOPATH="$(mktemp -d)" && git clone https://github.com/BurntSushi/toml.git "$GOPATH/src/github.com/BurntSushi/toml" && (cd "$GOPATH/src/github.com/BurntSushi/toml" && git checkout -q "$TOMLV_COMMIT") && go build -v -o /usr/local/bin/tomlv github.com/BurntSushi/toml/cmd/tomlv && rm -rf "$GOPATH" 
set -x && git clone https://github.com/akavel/rsrc.git /go/src/github.com/akavel/rsrc && cd /go/src/github.com/akavel/rsrc && git checkout -q $RSRC_COMMIT && go install -v 
```
============================
##2.Git clone to your gopath
    If you follow the above example to do, then the next step can be this:
    <code>git clone https://github.com/windowsair/docker-dev.git /go/src/github.com/docker/docker</code>
============================    
##3.Warning
  Remember to delete the original .git folder,and rename the git folder to .git .
============================  
##4.Start to hack
  Run <code>./hack/make.sh</code> and enjoy it !
 

