# datariver-platform-arm64

This repository contains a split tar.gz archive managed with Git LFS.

## Verify archive parts

```bash
shasum -a 256 -c datariver-platform-arm64.parts.sha256
```

### Restore

Run the following command in the directory containing all archive parts:

```
cat datariver-platform-arm64.tar.gz.part-* | tar -xzf -
```

This creates:

```
datariver-platform-arm64/
```

### Reestore to another directory

```
mkdir restore
cat datariver-platform-arm64.tar.gz.part-* | tar -xzf - -C restore
```
