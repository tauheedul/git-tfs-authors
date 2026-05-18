# git-tfs-authors

## Introduction
git-tfs-authors is a small utility to create an authors file, which you can use with the git-tfs clone command. It'll create a file with entries in the form of.

    DOMAIN\123123 = Doe, John <john.doe@company.com>
    DOMAIN\234234 = Doe, Jane <jane.doe@company.com>

## Synopsis

```
Command line arguments
	
-h, --help                 show this message and exit
-a, --authors=VALUE        the name of the output file
-k, --keep-empty-email     keep accounts without an e-mail address (default false)
-s, --sort                 sort the entries in the output file (default	false)
-t, --tfs-server-uri=VALUE the uri of the tfs server (e.g. http://tfs:8080/tfs)

Examples:
GitTfsAuthors --tfs-server-uri http://tfs:8080/tfs
GitTfsAuthors --tfs-server-uri http://tfs:8080/tfs --authors=output.txt
GitTfsAuthors --tfs-server-uri http://tfs:8080/tfs --authors=output.txt --sort
```

## Prerequisites
* You'll need Visual Studio 2017, 2019, 2022 to build, because of the reference to Microsoft.TeamFoundation.Client.dll
    
## See also
* git-tfs-clone: https://github.com/git-tfs/git-tfs/blob/master/doc/commands/clone.md

## Debugging

1. EnsureAuthenticated fails with TF31002 (HTTP 404 not found error) on first run

Symptom: the tool exits immediately with a TeamFoundationServiceUnavailableException / TF31002, and the inner exception is an HTTP 404. The TFS server URL is correct and reachable in a browser.

Cause: the current Windows session has no cached credential for the TFS host, so WinHTTP cannot perform silent NTLM authentication. The TFS client SDK reports this as TF31002 rather than an auth error.

```
Example error:

Microsoft.TeamFoundation.TeamFoundationServiceUnavailableException: 'TF31002: Unable to connect to this Azure DevOps Server: http://tfs:8080/tfs/defaultcollection.
Azure DevOps Server Url: http://tfs:8080/tfs/defaultcollection

- The name, port number, or protocol for the Azure DevOps Server is incorrect.
- The Azure DevOps Server is offline.
- The password has expired or is incorrect.
```

Fix: open the TFS web access URL (e.g. http://tfs:8080/tfs) in a browser as the same Windows user that will run the tool, and sign in if prompted. Re-run the tool from the same session.

2. The output text is not updated or is empty

Symptom: The script completes but the output text is blank or some users are absent.

Fix: Re-run using the attribute -k (or --keep-empty-email) to include accounts that have no email address recorded in TFS. By default these are filtered out. Confirm also that the current TFS user has read access to the Project Collection if the output is blank.

## Notes

For use with Team Foundation Server or Azure DevOps Server (on-premise). Not compatible with Azure DevOps Services — the underlying IGroupSecurityService API is not exposed in the cloud product.