---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183132"
---
## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska projektowego

W tej sekcji przechodzi przez konfigurowanie środowiska programistycznego. Obejmuje to tworzenie ASP.NET aplikacji MVC, dodawanie połączenia połączonych usług, dodawanie kontrolera i określanie wymaganych dyrektyw obszaru nazw.

### <a name="create-an-aspnet-mvc-app-project"></a>Tworzenie projektu aplikacji MVC ASP.NET

1. Otwórz program Visual Studio.

1. Z menu głównego wybierz **polecenie Plik** > **nowego** > **projektu**.

1. W oknie dialogowym **Nowy projekt** wybierz pozycję **Web** > **ASP.NET Web Application (.NET Framework).** W polu **Nazwa** określ **opcję StorageAspNet**. Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe nowego projektu](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. W oknie dialogowym **Nowa ASP.NET aplikacji sieci Web** wybierz pozycję **MVC**, a następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowa ASP.NET aplikacji sieci Web](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Łączenie się z kontem magazynu platformy Azure za pomocą połączonych usług

1. W **Eksploratorze rozwiązań**kliknij projekt prawym przyciskiem myszy.

1. Z menu kontekstowego wybierz polecenie **Dodaj** > **połączoną usługę**.

1. W oknie dialogowym **Połączone usługi** wybierz pozycję Cloud Storage with **Azure Storage**.

    ![Zrzut ekranu przedstawiający okno dialogowe Usługi połączone](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. W oknie dialogowym **Usługi Azure Storage** wybierz konto magazynu platformy Azure, które ma być używane w tym samouczku. Aby utworzyć nowe konto magazynu platformy Azure, wybierz pozycję **Utwórz nowe konto magazynu**i wypełnij formularz. Po wybraniu istniejącego konta magazynu lub utworzeniu nowego wybierz pozycję **Dodaj**. Program Visual Studio instaluje pakiet NuGet dla usługi Azure Storage i ciąg połączenia magazynu w **witrynie Web.config**.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **na zależności ,** wybierz pozycję **Zarządzaj pakietami NuGet**i dodaj odwołanie do pakietu NuGet do najnowszej wersji programu Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Aby dowiedzieć się, jak utworzyć konto magazynu w [witrynie Azure portal](https://portal.azure.com), zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Konto magazynu można również utworzyć przy użyciu [usługi Azure PowerShell,](../articles/storage/common/storage-powershell-guide-full.md) [interfejsu wiersza polecenia platformy Azure](../articles/storage/common/storage-azure-cli.md)lub usługi Azure Cloud [Shell.](../articles/cloud-shell/overview.md)
