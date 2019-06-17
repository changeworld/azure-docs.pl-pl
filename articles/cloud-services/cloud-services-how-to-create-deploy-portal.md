---
title: Jak utworzyć i wdrożyć usługę w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i wdrożyć usługę w chmurze przy użyciu witryny Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: a6cf2276da463f71f008c4bfb6eee4c232b18308
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61433762"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Jak utworzyć i wdrożyć usługę w chmurze
Azure portal udostępnia dwa sposoby tworzenia i wdrażania usługi w chmurze: *Szybkie tworzenie* i *tworzenie niestandardowe*.

W tym artykule wyjaśniono, jak szybkie tworzenie umożliwia tworzenie nowej usługi w chmurze, a następnie użyj **przekazywanie** Aby przekazać i wdrożyć pakiet usługi w chmurze na platformie Azure. Gdy ta metoda portalu Azure sprawia, że dostępne wygodne linki do ukończenia wszystkich wymagań, zgodnie z rzeczywistym. Jeśli wszystko będzie gotowe do wdrożenia usługi w chmurze, podczas jego tworzenia, możesz to zrobić zarówno w tym samym czasie za pomocą tworzenie niestandardowe.

> [!NOTE]
> Jeśli planujesz publikować usługi w chmurze z DevOps platformy Azure, korzystając z funkcji Szybkie tworzenie, a następnie skonfigurować publikowanie DevOps platformy Azure z szybkiego startu platformy Azure lub do pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [ciągłego dostarczania na platformie Azure przy użyciu DevOps platformy Azure][TFSTutorialForCloudService], lub zobacz Pomoc dotyczącą **— Szybki Start** strony.
>
>

## <a name="concepts"></a>Pojęcia
Trzy składniki są wymagane do wdrażania aplikacji jako usługa w chmurze na platformie Azure:

* **Definicja usługi**  
  Chmura pliku definicji usługi (csdef) definiuje model usługi, takie jak liczba ról.
* **Konfiguracja usługi**  
  Plik konfiguracji usługi w chmurze (cscfg) zawiera ustawienia konfiguracji w chmurze usługi i dla indywidualnych ról, w tym liczbę wystąpień roli.
* **Pakiet usługi**  
  Pakiet usługi (cspkg) zawiera kod aplikacji i konfiguracji i pliku definicji usługi.

Dowiedz się więcej na temat tych i jak utworzyć pakiet [tutaj](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Przygotowywanie aplikacji
Przed wdrożeniem usługi w chmurze, należy utworzyć pakiet usługi w chmurze (cspkg) od kodu aplikacji i pliku konfiguracji usługi cloud (cscfg). Zestaw Azure SDK udostępnia narzędzia do przygotowywania te pliki wymagane wdrożenie. Można zainstalować zestawu SDK z [pliki do pobrania Azure](https://azure.microsoft.com/downloads/) strony w języku, w którym chcesz tworzenia kodu aplikacji.

Trzy funkcje usługi w chmurze wymagają zastosowania takiej konfiguracji specjalnych przed wyeksportowaniem pakietu usług:

* Jeśli chcesz wdrożyć usługę w chmurze, która używa protokołu Secure Sockets Layer (SSL) do szyfrowania danych [skonfigurować aplikację](cloud-services-configure-ssl-certificate-portal.md#modify) dla protokołu SSL.
* Jeśli chcesz skonfigurować połączenia pulpitu zdalnego z wystąpień roli [konfigurowania ról](cloud-services-role-enable-remote-desktop-new-portal.md) za pomocą pulpitu zdalnego.
* Jeśli chcesz skonfigurować pełne monitorowanie usług cloud Services, należy włączyć diagnostyki Azure dla usługi w chmurze. *Minimalny monitorowania* (ustawienie domyślne monitorowanie poziomu) używa liczników wydajności, zebranych z systemów operacyjnych hosta dla wystąpień roli (maszyny wirtualne). *Pełne monitorowanie* zbiera dodatkowe metryki na podstawie danych wydajności w ramach wystąpienia roli, aby umożliwić dokładniejszego przeanalizowania problemy występujące podczas przetwarzania aplikacji. Aby dowiedzieć się, jak włączyć diagnostyki platformy Azure, zobacz [Włączanie diagnostyki na platformie Azure](cloud-services-dotnet-diagnostics.md).

Aby utworzyć usługę w chmurze w przypadku wdrożeń z ról sieci web lub ról procesów roboczych, musisz mieć [Utwórz pakiet usługi](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Jeśli nie zainstalowano zestawu SDK usługi Azure, kliknij przycisk **Zainstaluj zestaw Azure SDK** otworzyć [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/), a następnie pobrać zestaw SDK dla języka, w którym użytkownik nie chce tworzyć kod. (Będziesz mieć możliwość zrobić to później).
* Jeśli wszystkie wystąpienia roli jest wymagany certyfikat, należy utworzyć certyfikaty. Usługi w chmurze wymagają pliku pfx z kluczem prywatnym. Możesz przekazać certyfikaty na platformie Azure, jak utworzyć i wdrożyć usługę w chmurze.

## <a name="create-and-deploy"></a>Tworzenie i wdrażanie
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **Utwórz zasób > Compute**, a następnie przewiń w dół i kliknij **usługi w chmurze**.

    ![Publikowanie usług cloud Services](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. W nowym **usługi w chmurze** okienku, wprowadź wartość w **nazwy DNS**.
4. Utwórz nową **grupy zasobów** lub wybierz istniejącą grupę.
5. Wybierz **lokalizację**.
6. Kliknij przycisk **pakietu**. Spowoduje to otwarcie **przekaż pakiet** okienka. Wypełnij wymagane pola. Upewnij się, jeśli dowolny z ról zawiera pojedyncze wystąpienie, **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** jest zaznaczone.
7. Upewnij się, że **rozpocząć wdrażanie** jest zaznaczone.
8. Kliknij przycisk **OK** który spowoduje zamknięcie **przekaż pakiet** okienka.
9. Jeśli nie masz żadnych certyfikatów, można dodać, kliknij przycisk **Utwórz**.

    ![Publikowanie usług cloud Services](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Przekaż certyfikat
Jeśli pakiet wdrażania [skonfigurowany do używania certyfikatów](cloud-services-configure-ssl-certificate-portal.md#modify), możesz teraz przekazać certyfikat.

1. Wybierz **certyfikaty**, a następnie na **Dodaj certyfikaty** okienku wybierz plik PFX certyfikatu SSL, a następnie podaj **hasło** dla certyfikatu,
2. Kliknij przycisk **Dołącz certyfikat**, a następnie kliknij przycisk **OK** na **Dodaj certyfikaty** okienka.
3. Kliknij przycisk **Utwórz** na **usługi w chmurze** okienka. Po wdrożeniu osiągnęła **gotowe** stan, możesz przejść do następnych kroków.

    ![Publikowanie usług cloud Services](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Sprawdź wdrożenie zostało ukończone pomyślnie
1. Kliknij wystąpienie usługi w chmurze.

    Stan powinny pokazywać, że usługa jest **systemem**.
2. W obszarze **Essentials**, kliknij przycisk **adres URL witryny** można otworzyć usługi w chmurze w przeglądarce sieci web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Kolejne kroki
* [Konfiguracja ogólna usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą chmurze](cloud-services-how-to-manage-portal.md).
* Konfigurowanie [certyfikaty ssl](cloud-services-configure-ssl-certificate-portal.md).
