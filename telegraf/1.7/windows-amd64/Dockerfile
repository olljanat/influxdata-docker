FROM microsoft/windowsservercore as build

# $ProgressPreference: https://github.com/PowerShell/PowerShell/issues/2138#issuecomment-251261324
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

RUN $newPath = ('C:\telegraf;{0}' -f $env:PATH); \
    Write-Host ('Updating PATH: {0}' -f $newPath); \
    [Environment]::SetEnvironmentVariable('PATH', $newPath, [EnvironmentVariableTarget]::Machine);

ENV TELEGRAF_VERSION 1.7.4
RUN $url = ('https://dl.influxdata.com/telegraf/releases/telegraf-{0}_windows_amd64.zip' -f $env:TELEGRAF_VERSION); \
    Write-Host ('Downloading {0} ...' -f $url); \
    Invoke-WebRequest -Uri $url -OutFile 'telegraf.zip'; \
    $sha256 = 'bc0381a7e6f8e4c083ff01c1b0749ab6faed5d14e62ae80ddd7a4b830e9777e2'; \
    Write-Host ('Verifying sha256 ({0}) ...' -f $sha256); \
    if ((Get-FileHash telegraf.zip -Algorithm sha256).Hash -ne $sha256) { \
        Write-Host 'FAILED!'; \
        exit 1; \
    }; \
    Write-Host 'Expanding ...'; \
    Expand-Archive telegraf.zip -DestinationPath C:\; \
    Write-Host 'Removing ...'; \
    Remove-Item telegraf.zip -Force; \
    Write-Host 'Complete.';

FROM microsoft/nanoserver:1803
USER ContainerAdministrator
COPY --from=build ["/telegraf/","/telegraf/"]
WORKDIR /telegraf
ENTRYPOINT ["telegraf.exe","--config","c:\telegraf\telegraf.conf"]
