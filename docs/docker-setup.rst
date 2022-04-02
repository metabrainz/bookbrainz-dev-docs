
Running docker inside WSL
###################################

**First make sure Windows is up-to-date**

  * In the Windows search type ``Windows Update`` and click on `Windows Update setting`
  * You will see a green check and “You’re up to date” message.  If not click “Check for updates”,  You will need to **repeat this step until there is no update to install**. 
Next install WSL2
  * Install `WSL2 <https://docs.microsoft.com/en-us/windows/wsl/install-win10>`_ (Windows Subsystem for Linux Documentation).
  * From the Windows Search Type ``powershell`` then right-click on 'Windows PowerShell' and then 'Run as administrator'.
  * Click 'Yes' to allow PowerShell to make changes to your device.
  * In the Administrator Windows PowerShell window run the command ``wsl --install -d Ubuntu``.
  * Next enable the Virtual Machine Platform. 
  * In the Administrator Windows PowerShell run ``dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart``.
  * Download and install the `WSL2 Linux kernel update package for x64 machines <https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi>`_
  * Restart Windows.
  * Now from the Windows Search Type ``powershell`` then right-click on `Windows PowerShell` and then `Run as administrator`.
  * In the PowerShell window run ``wsl --set-default-version 2``.
  * Next install a Linux distribution from the `Microsoft Store <https://aka.ms/wslstore>`_. Ubuntu is recommended. This will take several minutes to download and install.
  * You will be asked to set up a Linux user. The same username as windows is recommended.
  * You will now be able to run Linux commands in the Ubuntu terminal window.
You can now install `Docker Desktop for Windows <https://docs.docker.com/docker-for-windows/install/>`_
  * Download the Docker Desktop for Windows installer from `docker-desktop page <https://www.docker.com/products/docker-desktop>`_
  * Run the installer.
  * Restart Windows.
  * Go to Windows and let Docker finish setting up.  This can take a few minutes depending on your machine.
  * You have now installed Docker on Windows 10 for local workstation development.
  * You can now proceed to the next step.
.. seealso:: 
  Click :doc:`installation` to continue to setting up BookBrainz on your local machine. 




