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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160736"
---
## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska deweloperskiego

W tej sekcji przedstawiono konfigurowanie środowiska deweloperskiego. Obejmuje to tworzenie aplikacji ASP.NET MVC, dodawanie połączenia usług połączonych, Dodawanie kontrolera i określając dyrektywy wymaganej przestrzeni nazw.

### <a name="create-an-aspnet-mvc-app-project"></a>Tworzenie projektu aplikacji platformy ASP.NET MVC

1. Otwórz program Visual Studio.

1. W menu głównym wybierz **pliku** > **New** > **projektu**.

1. W **nowy projekt** okno dialogowe, wybierz opcję **Web** > **aplikacji sieci Web platformy ASP.NET (.NET Framework)**. W **nazwa** określ **StorageAspNet**. Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowy projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. W **Nowa aplikacja internetowa ASP.NET** okno dialogowe, wybierz opcję **MVC**, a następnie wybierz pozycję **OK**.

    ![Zrzut ekranu z nowej aplikacji sieci Web ASP.NET, okno dialogowe](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Nawiązywanie połączenia z konta usługi Azure storage za pomocą usług połączonych

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt.

1. Z menu kontekstowego wybierz **Dodaj** > **podłączoną usługę**.

1. W **podłączone usługi** okno dialogowe, wybierz opcję **magazynu w chmurze z usługą Azure Storage**.

    ![Zrzut ekranu z połączonych usług, okno dialogowe](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. W **usługi Azure Storage** okno dialogowe, wybierz opcję konta magazynu platformy Azure ma być używany na potrzeby tego samouczka. Aby utworzyć nowe konto magazynu platformy Azure, wybierz **Utwórz nowe konto magazynu**i wypełnij formularz. Po wybraniu istniejące konto magazynu lub tworząc nową, wybierz **Dodaj**. Program Visual Studio instaluje pakiet NuGet dla usługi Azure Storage i parametry połączenia magazynu w celu **Web.config**.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **zależności**, wybierz **Zarządzaj pakietami NuGet**i Dodaj odwołanie do pakietu NuGet do najnowszej wersji programu Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Aby dowiedzieć się, jak utworzyć konto magazynu przy użyciu [witryny Azure portal](https://portal.azure.com), zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Można również utworzyć konto magazynu przy użyciu [programu Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [wiersza polecenia platformy Azure](../articles/storage/common/storage-azure-cli.md), lub [usługi Azure Cloud Shell](../articles/cloud-shell/overview.md).
