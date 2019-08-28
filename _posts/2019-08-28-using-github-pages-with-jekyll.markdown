```bash
# install the latest docker version
export JEKYLL_VERSION=latest
docker pull jekyll/jekyll:$JEKYLL_VERSION

# run fron the root directory of the repository serving GitHub pages
docker run --rm --volume="$PWD:/srv/jekyll" -it jekyll/jekyll:$JEKYLL_VERSION jekyll new .

# test site from http://localhost:4000
docker run --rm --volume="$PWD:/srv/jekyll" -p4000:4000 -it jekyll/jekyll:$JEKYLL_VERSION jekyll serve
```

