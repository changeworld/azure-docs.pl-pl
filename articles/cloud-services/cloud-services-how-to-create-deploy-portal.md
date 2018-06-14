---
title: Jak utworzyć i wdrożyć usługi w chmurze | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i wdrażania usługi w chmurze przy użyciu portalu Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 96b92690cd164b1012380f82a1d1bd3336350e57
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388088"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Jak utworzyć i wdrożyć usługi w chmurze
Azure portal udostępnia dwa sposoby tworzenia i wdrażania usługi w chmurze: *szybkie tworzenie* i *Utwórz niestandardowy*.

W tym artykule wyjaśniono, jak utworzyć nową usługę w chmurze, a następnie użyj przy użyciu metody szybkie tworzenie **przekazać** Aby przekazać i wdrożyć pakiet usługi chmury na platformie Azure. Korzystając z tej metody, portalu Azure sprawia, że dostępne wygodne łącza ukończenia wszystkich wymagań w trakcie pracy. Jeśli wszystko jest gotowe do wdrożenia usługi w chmurze podczas jego tworzenia, możesz to zrobić zarówno w tym samym czasie przy użyciu Utwórz niestandardowy.

> [!NOTE]
> Jeśli planujesz opublikować usługi w chmurze z programu Visual Studio Team Services (VSTS), użyj szybkiego tworzenia, a następnie skonfiguruj publikowanie programu VSTS z Szybki Start Azure lub do pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [ciągłego dostarczania danych do platformy Azure przy użyciu programu Visual Studio Team Services][TFSTutorialForCloudService], lub można znaleźć w Pomocy **Szybki Start** strony.
>
>

## <a name="concepts"></a>Pojęcia
Trzy składniki są wymagane do wdrażania aplikacji jako usługa w chmurze na platformie Azure:

* **Definicja usługi**  
  Chmura pliku definicji usługi (csdef) definiuje modelu usługi, w tym liczba ról.
* **Konfiguracja usługi**  
  Plik konfiguracji usługi chmury (cscfg) zawiera ustawienia konfiguracji dla chmury usługi i poszczególne role, w tym liczbę wystąpień roli.
* **Pakiet usługi**  
  Pakiet usługi (cspkg) zawiera kod aplikacji i konfiguracji i pliku definicji usługi.

Dowiedz się więcej na temat tych i tworzenie pakietu [tutaj](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Przygotowanie aplikacji
Przed wdrożeniem usługi w chmurze, należy utworzyć pakiet usługi chmury (cspkg) w kodzie aplikacji i pliku konfiguracji usługi chmury (cscfg). Zestaw Azure SDK oferuje narzędzia przygotowania te pliki wymagane wdrożenie. Można zainstalować zestawu SDK [Azure pobiera](https://azure.microsoft.com/downloads/) strony w języku wolisz do opracowywania kod aplikacji.

Trzy funkcje usługi chmury wymagają specjalnych konfiguracji przed wyeksportowaniem pakietu usług:

* Jeśli ma zostać wdrożona usługa w chmurze, która używa protokołu Secure Sockets Layer (SSL) do szyfrowania danych [konfigurowania aplikacji](cloud-services-configure-ssl-certificate-portal.md#modify) dla protokołu SSL.
* Jeśli chcesz skonfigurować połączenia pulpitu zdalnego z wystąpień roli [konfigurowania ról](cloud-services-role-enable-remote-desktop-new-portal.md) dla pulpitu zdalnego.
* Jeśli chcesz skonfigurować pełne, monitorowanie dla usługi w chmurze, należy włączyć diagnostyki Azure dla usługi w chmurze. *Monitorowanie minimalnego* (domyślne monitorowania poziomu) używa liczniki wydajności zebrane z hosta systemów operacyjnych dla wystąpień roli (maszyny wirtualne). *Monitorowanie pełne* zbiera dodatkowe metryki na podstawie danych wydajności w ramach wystąpienia roli, aby umożliwić analizę bliżej problemów występujących podczas przetwarzania aplikacji. Aby dowiedzieć się, jak włączyć diagnostyki Azure, zobacz [Włączanie diagnostyki na platformie Azure](cloud-services-dotnet-diagnostics.md).

Aby utworzyć usługi w chmurze z wdrożeniami ról sieć web i proces roboczy ról, należy [Utwórz pakiet usługi](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Jeśli nie zainstalowano zestaw SDK usługi Azure, kliknij przycisk **Zainstaluj zestaw Azure SDK** otworzyć [strona plików do pobrania usługi Azure](https://azure.microsoft.com/downloads/), a następnie pobrać zestaw SDK dla języka wolisz tworzenia kodu. (Będziesz mieć możliwość zrobić to później).
* Jeśli wszystkie wystąpienia roli jest wymagany certyfikat, należy utworzyć certyfikaty. Usługi w chmurze wymagają pliku pfx z kluczem prywatnym. Jak utworzyć i wdrożyć usługę w chmurze, możesz przekazać certyfikaty na platformie Azure.

## <a name="create-and-deploy"></a>Tworzenie i wdrażanie
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **tworzenie zasobu > obliczeniowe**, następnie przewiń w dół i kliknij przycisk **usługi w chmurze**.

    ![Publikowanie usługi w chmurze](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. W nowym **usługi w chmurze** okienku, wprowadź wartość dla **nazwy DNS**.
4. Utwórz nową **grupy zasobów** lub wybierz istniejący.
5. Wybierz **lokalizację**.
6. Kliknij przycisk **pakietu**. Spowoduje to otwarcie **przekazania pakietu** okienka. Wypełnij wymagane pola. Jeśli jakieś role zawierają pojedynczego wystąpienia, upewnij się, **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** jest zaznaczone.
7. Upewnij się, że **rozpocząć wdrażanie** jest zaznaczone.
8. Kliknij przycisk **OK** którego zostanie zamknięte **przekazania pakietu** okienka.
9. Jeśli nie masz żadnych certyfikatów, aby dodać, kliknij przycisk **Utwórz**.

    ![Publikowanie usługi w chmurze](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Przekaż certyfikat
Jeśli pakiet wdrażania [skonfigurowana do używania certyfikatów](cloud-services-configure-ssl-certificate-portal.md#modify), możesz teraz przekazać certyfikat.

1. Wybierz **certyfikaty**i na **Dodaj certyfikaty** okienku, wybierz plik .pfx certyfikatu SSL, a następnie podaj **hasło** dla certyfikatu
2. Kliknij przycisk **certyfikatu Attach**, a następnie kliknij przycisk **OK** na **Dodaj certyfikaty** okienka.
3. Kliknij przycisk **Utwórz** na **usługi w chmurze** okienka. Po wdrożeniu został osiągnięty **gotowe** stanu, możesz przejść do następnych kroków.

    ![Publikowanie usługi w chmurze](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Sprawdź wdrożenia ukończone pomyślnie
1. Kliknij przycisk wystąpienie usługi w chmurze.

    Stan powinny wskazywać, że usługa jest **systemem**.
2. W obszarze **Essentials**, kliknij przycisk **adres URL witryny** można otworzyć usługi w chmurze w przeglądarce sieci web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Kolejne kroki
* [Konfiguracja ogólna usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Skonfiguruj [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Usługi w chmurze zarządzanie](cloud-services-how-to-manage-portal.md).
* Skonfiguruj [certyfikaty ssl](cloud-services-configure-ssl-certificate-portal.md).
