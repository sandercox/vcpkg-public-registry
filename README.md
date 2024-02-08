# Upgrade vcpkg and FFmpeg

As we use a custom modified version of FFmpeg, we need to upgrade this port manually. We still like to use the updates from upstream vcpkg so we need to extract those changes and apply our changes on top of that.

## Extract changes from vcpkg

```bash
# Inside the vcpkg repo
git subtree split -P ports/ffmpeg -b ffmpeg-port

# checkout our public registry
git clone https://github.com/resolume/vcpkg-public-registry.git
cd vcpkg-public-registry

# assuming our modifications on ffmpeg are latest commit on master
git checkout -B ffmpeg-port master^1
git subtree pull <path-to-vcpkg-repo> ffmpeg-port --prefix ports/ffmpeg
git checkout -B ffmpeg-update master
git rebase -i --autosquash ffmpeg-port

### Fix conflicts (probably version in vcpkg.json)

# update version files
git rev-parse HEAD:ports/ffmpeg

# copy this commit and update `versions/f-/ffmpeg.json` to include this version (with new port file version)

# update baseline.json port-version to match

# Then commit these changes
git commit --amend --no-edit versions
git remote add forpr git@github.com/sandercox/vcpkg-public-registry.git
git push forpr ffmpeg-update

# Head over to github to start a PR with these changes
```
