# escape=`

FROM mcr.microsoft.com/windows/servercore:ltsc2016

# Install .NET Fx 3.5
RUN powershell -Command `
        $ErrorActionPreference = 'Stop'; `
        $ProgressPreference = 'SilentlyContinue'; `
        Invoke-WebRequest `
            -UseBasicParsing `
            -Uri "https://dotnetbinaries.blob.core.windows.net/dockerassets/microsoft-windows-netfx3-ltsc2016.zip" `
            -OutFile microsoft-windows-netfx3.zip; `
        Expand-Archive microsoft-windows-netfx3.zip; `
    && del microsoft-windows-netfx3.zip `
    && dism /Online /Quiet /Add-Package /PackagePath:C:\microsoft-windows-netfx3\microsoft-windows-netfx3-ondemand-package.cab `
    && rmdir /S /Q microsoft-windows-netfx3 `
    && powershell Remove-Item -Force -Recurse ${Env:TEMP}\*

# Apply latest patch
RUN powershell -Command `
        $ProgressPreference = 'SilentlyContinue'; `
        Invoke-WebRequest `
            -UseBasicParsing `
            -Uri "http://download.windowsupdate.com/d/msdownload/update/software/secu/2020/02/windows10.0-kb4537764-x64_93e41ada5a984e3749ecd87bc5515bdc48cefb4d.msu" `
            -OutFile patch.msu; `
    && mkdir patch `
    && expand patch.msu patch -F:* `
    && del patch.msu `
    && dism /Online /Quiet /Add-Package /PackagePath:C:\patch\windows10.0-kb4537764-x64.cab `
    && rmdir /S /Q patch

# ngen .NET Fx
ENV COMPLUS_NGenProtectedProcess_FeatureEnabled 0
RUN \Windows\Microsoft.NET\Framework64\v4.0.30319\ngen update `
    && \Windows\Microsoft.NET\Framework\v4.0.30319\ngen update `
    && \Windows\Microsoft.NET\Framework64\v2.0.50727\ngen update `
    && \Windows\Microsoft.NET\Framework\v2.0.50727\ngen update
