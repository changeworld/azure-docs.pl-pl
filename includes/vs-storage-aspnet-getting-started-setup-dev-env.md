## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska deweloperskiego

Ta sekcja przeprowadzi Cię przez Konfigurowanie środowiska deweloperskiego. W tym tworzenie aplikacji ASP.NET MVC, dodawanie połączenia usług połączonych dodawania kontrolera i określanie dyrektywy wymaganej przestrzeni nazw.

### <a name="create-an-aspnet-mvc-app-project"></a>Utwórz projekt aplikacji platformy ASP.NET MVC

1. Otwórz program Visual Studio.

1. Wybierz z menu głównego **pliku** > **nowy** > **projektu**.

1. W **nowy projekt** okno dialogowe, wybierz opcję **Web** > **aplikacji sieci Web platformy ASP.NET (.NET Framework)**. W **nazwa** określ **StorageAspNet**. Kliknij przycisk **OK**.

    ![Okno dialogowe zrzut ekranu nowego projektu](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. W **nowej aplikacji sieci Web ASP.NET** okno dialogowe, wybierz opcję **MVC**, a następnie wybierz **OK**.

    ![Okno dialogowe zrzut ekranu z nowej aplikacji sieci Web ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Użyj podłączonych usług do łączenia się z kontem magazynu platformy Azure

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt.

2. Wybierz z menu kontekstowego **Dodaj** > **podłączonej usługi**.

1. W **usług połączonych** okno dialogowe, wybierz opcję **magazynu w chmurze z usługą Azure Storage**.

    ![Zrzut ekranu połączona usług — okno dialogowe](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. W **usługi Azure Storage** okno dialogowe, wybierz konto magazynu Azure do użycia na potrzeby tego samouczka. Aby utworzyć nowe konto magazynu platformy Azure, wybierz **Utwórz nowe konto magazynu**i wypełnij formularz. Po wybraniu istniejącego konta magazynu lub tworzenia nowej, wybierz **Dodaj**. Visual Studio instaluje pakiet NuGet dla usługi Azure Storage i parametry połączenia magazynu **Web.config**.

> [!TIP]
> Aby dowiedzieć się, jak utworzyć konto magazynu z [portalu Azure](https://portal.azure.com), zobacz [Utwórz konto magazynu](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Można również utworzyć konto magazynu przy użyciu [programu Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [interfejsu wiersza polecenia Azure](../articles/storage/common/storage-azure-cli.md), lub [powłoki chmury Azure](../articles/cloud-shell/overview.md).

