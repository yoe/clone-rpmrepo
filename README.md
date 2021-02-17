# clone-rpmrepo

## about

This tool creates a local mirror of RPM-MD repositories (repomd.xml). In
doing so, it uses the following algorithm:

- Download the `repomd.xml` file to a temporary name
- If a `repomd.xml.asc` and a `repomd.xml.key` file exist, download
  those to a temporary name, too.
- Parse `repomd.xml`, and ensure that all files that it links to, exist
  and match the checksum in `repomd.xml`. If they don't exist, or the
  checksum doesn't match, download the file.
- Parse the `filelists` XML file, and do the same for all the files that
  it links to.
- If the `repomd.xml` file mentions a `deltainfo` file, do the same for
  all files in that file.
- Move the temporary `repomd.xml` file as well as the `.key` and `.asc`
  files (if they exist) to the original name.
- Finally, go over all the files in the local directory, removing any
  files and directories that were not referenced from any of the XML files
  downloaded previously.

This keeps the repository internally consistent while the files are
being downloaded, and removes all files that are no longer needed.

## How to

Assuming a `repomd.xml` file at URL
`https://repo.url/repository/repodata/repomd.xml` that we want to mirror
to `/srv/repository/repodata/repomd.xml` (with all relevant files
relative to `repomd.xml` copied in their correct location), you should
run this tool like so:

    clone-rpmrepo https://repo.url/repository /srv/repository

Then wait while files are being downloaded.

When you want to update the mirror at a later point in time, you can
rerun it the same way.
