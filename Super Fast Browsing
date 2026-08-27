# ==============================================================
# WINDOWS 11 - MULTI-BROWSER PRIVACY HARDENING
#
# Automatic:
#   Google Chrome
#   Brave
#   Microsoft Edge
#   Mozilla Firefox
#   Opera
#   Opera GX
#   Avast Secure Browser
#
# Special handling:
#   AdsPower SunBrowser -> detected, use AdsPower controls
#   Safari for Windows  -> detected, warning only (obsolete)
#
# RUN POWERSHELL AS ADMINISTRATOR
# ==============================================================


# ==============================================================
# SETTINGS
# ==============================================================

# TRUE = Disable WebGL / 3D APIs where supported.
# This gives stronger fingerprint protection but can break:
# Google Maps 3D, browser games, WebGL apps, etc.

$StrongMode = $true


# ==============================================================
# ADMIN CHECK
# ==============================================================

$Identity = [Security.Principal.WindowsIdentity]::GetCurrent()

$Principal = New-Object `
    Security.Principal.WindowsPrincipal($Identity)

$IsAdmin = $Principal.IsInRole(
    [Security.Principal.WindowsBuiltInRole]::Administrator
)

if (-not $IsAdmin) {

    Write-Host ""
    Write-Host "==============================================" -ForegroundColor Red
    Write-Host " RUN POWERSHELL AS ADMINISTRATOR"
    Write-Host "==============================================" -ForegroundColor Red
    Write-Host ""

    exit
}


Write-Host ""
Write-Host "==================================================" -ForegroundColor Cyan
Write-Host " WINDOWS 11 - BROWSER PRIVACY HARDENING"
Write-Host "==================================================" -ForegroundColor Cyan
Write-Host ""


# ==============================================================
# CLOSE NORMAL BROWSERS
# ==============================================================

Write-Host "Closing browsers..." -ForegroundColor Yellow


$BrowserProcesses = @(

    "chrome",
    "brave",
    "msedge",
    "firefox",
    "opera",
    "opera_gx",
    "AvastBrowser"

)


foreach ($ProcessName in $BrowserProcesses) {

    Get-Process `
        -Name $ProcessName `
        -ErrorAction SilentlyContinue |
    Stop-Process `
        -Force `
        -ErrorAction SilentlyContinue
}


Start-Sleep -Seconds 2



# ==============================================================
# HELPER - CHROMIUM POLICY
# ==============================================================

function Set-ChromiumPrivacyPolicy {

    param(

        [Parameter(Mandatory = $true)]
        [string]$Name,

        [Parameter(Mandatory = $true)]
        [string]$RegistryPath,

        [string]$WebRTCPolicyName = "WebRtcIPHandling"

    )


    Write-Host ""
    Write-Host "Configuring $Name..." -ForegroundColor Green


    New-Item `
        -Path $RegistryPath `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # GEOLOCATION
    # 2 = Block
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultGeolocationSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # MOTION / LIGHT / ORIENTATION SENSORS
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultSensorsSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # LOCAL FONT ENUMERATION
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultLocalFontsSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # WEB USB
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultWebUsbGuardSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # WEB BLUETOOTH
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultWebBluetoothGuardSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # WEB HID
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultWebHidGuardSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # SERIAL PORTS
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultSerialGuardSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # FILE SYSTEM READ API
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultFileSystemReadGuardSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # FILE SYSTEM WRITE API
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultFileSystemWriteGuardSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # IDLE DETECTION
    # Prevent websites asking whether computer/user is idle
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultIdleDetectionSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # WINDOW / MULTI-MONITOR MANAGEMENT
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name "DefaultWindowManagementSetting" `
        -Value 2 `
        -PropertyType DWord `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # WEBRTC IP PROTECTION
    # Prevent direct non-proxied UDP path
    # ----------------------------------------------------------

    New-ItemProperty `
        -Path $RegistryPath `
        -Name $WebRTCPolicyName `
        -Value "disable_non_proxied_udp" `
        -PropertyType String `
        -Force |
    Out-Null



    # ----------------------------------------------------------
    # OPTIONAL STRONG MODE
    # Disable WebGL / 3D APIs
    # ----------------------------------------------------------

    if ($StrongMode) {

        New-ItemProperty `
            -Path $RegistryPath `
            -Name "Disable3DAPIs" `
            -Value 1 `
            -PropertyType DWord `
            -Force |
        Out-Null

    }


    Write-Host "  DONE" -ForegroundColor Green
}



# ==============================================================
# GOOGLE CHROME
# ==============================================================

Set-ChromiumPrivacyPolicy `
    -Name "Google Chrome" `
    -RegistryPath "HKLM:\SOFTWARE\Policies\Google\Chrome"



# ==============================================================
# BRAVE
# ==============================================================

Set-ChromiumPrivacyPolicy `
    -Name "Brave" `
    -RegistryPath "HKLM:\SOFTWARE\Policies\BraveSoftware\Brave"



# ==============================================================
# MICROSOFT EDGE
#
# Edge uses a different WebRTC policy name.
# ==============================================================

Set-ChromiumPrivacyPolicy `
    -Name "Microsoft Edge" `
    -RegistryPath "HKLM:\SOFTWARE\Policies\Microsoft\Edge" `
    -WebRTCPolicyName "WebRtcLocalhostIpHandling"



# ==============================================================
# FIREFOX
# ==============================================================

Write-Host ""
Write-Host "Configuring Firefox..." -ForegroundColor Green


$FirefoxPath = "HKLM:\SOFTWARE\Policies\Mozilla\Firefox"


New-Item `
    -Path $FirefoxPath `
    -Force |
Out-Null



# Disable Firefox telemetry

New-ItemProperty `
    -Path $FirefoxPath `
    -Name "DisableTelemetry" `
    -Value 1 `
    -PropertyType DWord `
    -Force |
Out-Null



# Disable experimental/study enrollment

New-ItemProperty `
    -Path $FirefoxPath `
    -Name "DisableFirefoxStudies" `
    -Value 1 `
    -PropertyType DWord `
    -Force |
Out-Null



# --------------------------------------------------------------
# FIREFOX LOCKED PREFERENCES
# --------------------------------------------------------------

$FirefoxPreferenceObject = [ordered]@{


    # Anti fingerprinting
    "privacy.resistFingerprinting" = @{

        Value  = $true
        Status = "locked"
        Type   = "boolean"

    }


    # Window size normalization
    "privacy.resistFingerprinting.letterboxing" = @{

        Value  = $true
        Status = "locked"
        Type   = "boolean"

    }


    # Disable WebRTC completely
    "media.peerconnection.enabled" = @{

        Value  = $false
        Status = "locked"
        Type   = "boolean"

    }


    # Disable browser geolocation
    "geo.enabled" = @{

        Value  = $false
        Status = "locked"
        Type   = "boolean"

    }


    # Disable device sensors
    "device.sensors.enabled" = @{

        Value  = $false
        Status = "locked"
        Type   = "boolean"

    }


    # Disable MIDI hardware interface
    "dom.webmidi.enabled" = @{

        Value  = $false
        Status = "locked"
        Type   = "boolean"

    }

}



if ($StrongMode) {

    $FirefoxPreferenceObject["webgl.disabled"] = @{

        Value  = $true
        Status = "locked"
        Type   = "boolean"

    }

}



$FirefoxJSON = $FirefoxPreferenceObject |
    ConvertTo-Json `
        -Depth 10 `
        -Compress



New-ItemProperty `
    -Path $FirefoxPath `
    -Name "Preferences" `
    -Value @($FirefoxJSON) `
    -PropertyType MultiString `
    -Force |
Out-Null



Write-Host "  DONE" -ForegroundColor Green



# ==============================================================
# JSON HELPERS FOR OPERA / AVAST
# ==============================================================

function Ensure-JsonObject {

    param(

        [Parameter(Mandatory = $true)]
        $Parent,

        [Parameter(Mandatory = $true)]
        [string]$Name

    )


    if (-not $Parent.PSObject.Properties[$Name]) {

        $Parent |
        Add-Member `
            -MemberType NoteProperty `
            -Name $Name `
            -Value ([PSCustomObject]@{})

    }


    return $Parent.$Name
}



function Set-JsonValue {

    param(

        [Parameter(Mandatory = $true)]
        $Object,

        [Parameter(Mandatory = $true)]
        [string]$Name,

        [Parameter(Mandatory = $true)]
        $Value

    )


    if ($Object.PSObject.Properties[$Name]) {

        $Object.$Name = $Value

    }

    else {

        $Object |
        Add-Member `
            -MemberType NoteProperty `
            -Name $Name `
            -Value $Value

    }
}



# ==============================================================
# MODIFY CHROMIUM PROFILE PREFERENCES
#
# Used only when reliable enterprise policy management isn't
# available to us.
# ==============================================================

function Protect-ChromiumPreferences {

    param(

        [Parameter(Mandatory = $true)]
        [string]$BrowserName,

        [Parameter(Mandatory = $true)]
        [string[]]$RootPaths

    )


    Write-Host ""
    Write-Host "Configuring $BrowserName profiles..." -ForegroundColor Green


    $PreferenceFiles = @()


    foreach ($RootPath in $RootPaths) {

        if (-not (Test-Path $RootPath)) {

            continue

        }


        # Main profile

        $MainPreferenceFile = Join-Path `
            $RootPath `
            "Preferences"


        if (Test-Path $MainPreferenceFile) {

            $PreferenceFiles += $MainPreferenceFile

        }


        # Default profile

        $DefaultPreferenceFile = Join-Path `
            $RootPath `
            "Default\Preferences"


        if (Test-Path $DefaultPreferenceFile) {

            $PreferenceFiles += $DefaultPreferenceFile

        }


        # Additional Chromium profiles

        Get-ChildItem `
            -Path $RootPath `
            -Directory `
            -ErrorAction SilentlyContinue |
        Where-Object {

            $_.Name -like "Profile *"

        } |
        ForEach-Object {

            $P = Join-Path `
                $_.FullName `
                "Preferences"


            if (Test-Path $P) {

                $PreferenceFiles += $P

            }
        }

    }



    $PreferenceFiles = $PreferenceFiles |
        Select-Object -Unique



    if (-not $PreferenceFiles) {

        Write-Host "  No profile Preferences files found." -ForegroundColor DarkYellow

        return
    }



    foreach ($PreferenceFile in $PreferenceFiles) {

        Write-Host "  Profile:"
        Write-Host "    $PreferenceFile"


        # ------------------------------------------------------
        # BACKUP
        # ------------------------------------------------------

        $TimeStamp = Get-Date -Format "yyyyMMdd-HHmmss"


        $BackupFile = "$PreferenceFile.PrivacyBackup-$TimeStamp"


        Copy-Item `
            -Path $PreferenceFile `
            -Destination $BackupFile `
            -Force



        try {

            $Raw = [System.IO.File]::ReadAllText(
                $PreferenceFile
            )


            $Prefs = $Raw |
                ConvertFrom-Json `
                    -ErrorAction Stop



            # --------------------------------------------------
            # CONTENT SETTINGS
            # --------------------------------------------------

            $Profile = Ensure-JsonObject `
                -Parent $Prefs `
                -Name "profile"


            $ContentSettings = Ensure-JsonObject `
                -Parent $Profile `
                -Name "default_content_setting_values"



            # Block location

            Set-JsonValue `
                $ContentSettings `
                "geolocation" `
                2



            # Block sensors

            Set-JsonValue `
                $ContentSettings `
                "sensors" `
                2



            # Block local font access

            Set-JsonValue `
                $ContentSettings `
                "local_fonts" `
                2



            # USB

            Set-JsonValue `
                $ContentSettings `
                "usb_guard" `
                2



            # Bluetooth

            Set-JsonValue `
                $ContentSettings `
                "bluetooth_guard" `
                2



            # HID

            Set-JsonValue `
                $ContentSettings `
                "hid_guard" `
                2



            # Serial

            Set-JsonValue `
                $ContentSettings `
                "serial_guard" `
                2



            # MIDI

            Set-JsonValue `
                $ContentSettings `
                "midi_sysex" `
                2



            # File-system read

            Set-JsonValue `
                $ContentSettings `
                "file_system_read_guard" `
                2



            # File-system write

            Set-JsonValue `
                $ContentSettings `
                "file_system_write_guard" `
                2



            # Idle detection

            Set-JsonValue `
                $ContentSettings `
                "idle_detection" `
                2



            # Window / monitor management

            Set-JsonValue `
                $ContentSettings `
                "window_management" `
                2



            # --------------------------------------------------
            # WEBRTC
            # --------------------------------------------------

            $WebRTC = Ensure-JsonObject `
                -Parent $Prefs `
                -Name "webrtc"



            Set-JsonValue `
                $WebRTC `
                "ip_handling_policy" `
                "disable_non_proxied_udp"



            Set-JsonValue `
                $WebRTC `
                "multiple_routes_enabled" `
                $false



            Set-JsonValue `
                $WebRTC `
                "nonproxied_udp_enabled" `
                $false



            # --------------------------------------------------
            # WRITE FILE WITHOUT UTF-8 BOM
            # --------------------------------------------------

            $NewJson = $Prefs |
                ConvertTo-Json `
                    -Depth 100



            $UTF8NoBOM = New-Object `
                System.Text.UTF8Encoding($false)



            [System.IO.File]::WriteAllText(

                $PreferenceFile,
                $NewJson,
                $UTF8NoBOM

            )



            # Validate written JSON

            [System.IO.File]::ReadAllText(
                $PreferenceFile
            ) |
            ConvertFrom-Json |
            Out-Null



            Write-Host "    Hardened successfully." -ForegroundColor Green
            Write-Host "    Backup: $BackupFile"

        }

        catch {

            Write-Host "    ERROR: $($_.Exception.Message)" -ForegroundColor Red
            Write-Host "    Restoring backup..."


            Copy-Item `
                -Path $BackupFile `
                -Destination $PreferenceFile `
                -Force

        }

    }

}



# ==============================================================
# OPERA + OPERA GX
# ==============================================================

$OperaRoots = @(

    (Join-Path $env:APPDATA "Opera Software\Opera Stable"),
    (Join-Path $env:APPDATA "Opera Software\Opera GX Stable")

)


Protect-ChromiumPreferences `
    -BrowserName "Opera / Opera GX" `
    -RootPaths $OperaRoots



# ==============================================================
# AVAST SECURE BROWSER
# ==============================================================

$AvastRoots = @(

    (Join-Path `
        $env:LOCALAPPDATA `
        "AVAST Software\Browser\User Data")

)


Protect-ChromiumPreferences `
    -BrowserName "Avast Secure Browser" `
    -RootPaths $AvastRoots



# ==============================================================
# ADSPOWER / SUNBROWSER
#
# Do NOT alter fingerprint-profile files externally.
# AdsPower controls these internally.
# ==============================================================

Write-Host ""
Write-Host "Checking AdsPower / SunBrowser..." -ForegroundColor Green


$AdsPowerRunning = Get-Process `
    -ErrorAction SilentlyContinue |
Where-Object {

    $_.ProcessName -match "adspower|sunbrowser"

}


if ($AdsPowerRunning) {

    Write-Host "  AdsPower/SunBrowser detected." -ForegroundColor Yellow

}

else {

    Write-Host "  No active AdsPower/SunBrowser process detected."

}


Write-Host ""
Write-Host "  Configure these INSIDE AdsPower:" -ForegroundColor Yellow
Write-Host ""
Write-Host "    Timezone     : Based on IP"
Write-Host "    Location     : Block, or Based on IP if required"
Write-Host "    WebRTC       : Disabled UDP"
Write-Host ""
Write-Host "  Also enable:"
Write-Host "    Real-time match timezone/location when dynamic IP changes"
Write-Host ""



# ==============================================================
# SAFARI FOR WINDOWS
# ==============================================================

Write-Host ""
Write-Host "Checking Safari..." -ForegroundColor Green


$SafariLocations = @(

    "$env:ProgramFiles\Safari\Safari.exe",
    "${env:ProgramFiles(x86)}\Safari\Safari.exe"

)


$SafariFound = $false


foreach ($SafariPath in $SafariLocations) {

    if (
        $SafariPath -and
        (Test-Path $SafariPath)
    ) {

        $SafariFound = $true

        Write-Host ""
        Write-Host "  Safari found:" -ForegroundColor Red
        Write-Host "  $SafariPath"

    }

}


if ($SafariFound) {

    Write-Host ""
    Write-Host "  WARNING:" -ForegroundColor Red
    Write-Host "  Safari for Windows cannot be safely hardened"
    Write-Host "  using current Apple security/policy controls."
    Write-Host ""
    Write-Host "  Apple no longer updates Safari for Windows."
    Write-Host "  Do NOT use it for banking, email, payments,"
    Write-Host "  passwords, or sensitive accounts."

}

else {

    Write-Host "  Safari for Windows not found."

}



# ==============================================================
# REFRESH POLICY
# ==============================================================

Write-Host ""
Write-Host "Refreshing Windows computer policies..." -ForegroundColor Yellow


gpupdate /target:computer /force |
Out-Null



# ==============================================================
# SUMMARY
# ==============================================================

Write-Host ""
Write-Host "==================================================" -ForegroundColor Cyan
Write-Host " PRIVACY HARDENING COMPLETE"
Write-Host "==================================================" -ForegroundColor Cyan

Write-Host ""

Write-Host "Chrome           : Protected"
Write-Host "Brave            : Protected"
Write-Host "Edge             : Protected"
Write-Host "Firefox          : Protected"
Write-Host "Opera            : Protected where profile found"
Write-Host "Opera GX         : Protected where profile found"
Write-Host "Avast Browser    : Protected where profile found"
Write-Host "SunBrowser       : Use AdsPower controls"
Write-Host "Safari Windows   : Unsupported / obsolete"

Write-Host ""

Write-Host "Strong Mode      : $StrongMode"

Write-Host ""

Write-Host "=================================================="
Write-Host " VERIFY POLICIES"
Write-Host "=================================================="

Write-Host ""
Write-Host "Chrome:"
Write-Host "  chrome://policy"

Write-Host ""
Write-Host "Brave:"
Write-Host "  brave://policy"

Write-Host ""
Write-Host "Edge:"
Write-Host "  edge://policy"

Write-Host ""
Write-Host "Firefox:"
Write-Host "  about:policies"

Write-Host ""
Write-Host "Opera:"
Write-Host "  opera://settings/content"

Write-Host ""
Write-Host "Avast:"
Write-Host "  secure://settings/content"

Write-Host ""

Write-Host "Restart your browsers now." -ForegroundColor Green
Write-Host ""
