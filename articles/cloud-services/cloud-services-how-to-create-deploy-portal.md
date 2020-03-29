---
title: Jak utworzyć i wdrożyć usługę w chmurze | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć i wdrożyć usługę w chmurze za pomocą witryny Azure Portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 53f53976b20359afc45abe1b25ca60325b5d6a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386174"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Jak utworzyć i wdrożyć usługę w chmurze
Portal Azure zawiera dwa sposoby tworzenia i wdrażania usługi w chmurze: *Szybkie tworzenie* i *tworzenie niestandardowe*.

W tym artykule wyjaśniono, jak użyć metody szybkiego tworzenia, aby utworzyć nową usługę w chmurze, a następnie użyć **przekaż** do przekazania i wdrożenia pakietu usługi w chmurze na platformie Azure. Korzystając z tej metody, witryna Azure portal udostępnia wygodne łącza do wypełniania wszystkich wymagań w miarę użycia. Jeśli chcesz wdrożyć usługę w chmurze podczas jej tworzenia, możesz wykonać obie te usługi w tym samym czasie przy użyciu tworzenia niestandardowego.

> [!NOTE]
> Jeśli planujesz opublikować usługę w chmurze z usługi Azure DevOps, użyj szybkiego tworzenia, a następnie skonfiguruj publikowanie programów Azure DevOps z usługi Szybki start platformy Azure lub pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [ciągłe dostarczanie na platformę Azure przy użyciu usługi Azure DevOps][TFSTutorialForCloudService]lub zobacz pomoc dla strony **Szybki start.**
>
>

## <a name="concepts"></a>Pojęcia
Do wdrożenia aplikacji jako usługi w chmurze na platformie Azure wymagane są trzy składniki:

* **Definicja usługi**  
  Plik definicji usługi w chmurze (csdef) definiuje model usługi, w tym liczbę ról.
* **Konfiguracja usługi**  
  Plik konfiguracji usługi w chmurze (cscfg) zawiera ustawienia konfiguracji usługi w chmurze i poszczególnych ról, w tym liczbę wystąpień roli.
* **Pakiet serwisowy**  
  Pakiet usługi (.cspkg) zawiera kod aplikacji i konfiguracje oraz plik definicji usługi.

Możesz dowiedzieć się więcej o tych i jak utworzyć pakiet [tutaj](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Przygotowanie aplikacji
Przed wdrożeniem usługi w chmurze należy utworzyć pakiet usługi w chmurze (.cspkg) z kodu aplikacji i pliku konfiguracji usługi w chmurze (cscfg). Zestaw Azure SDK udostępnia narzędzia do przygotowywania tych wymaganych plików wdrażania. Zestaw SDK można zainstalować na stronie [Usługi Azure Downloads](https://azure.microsoft.com/downloads/) w języku, w którym wolisz opracować kod aplikacji.

Trzy funkcje usługi w chmurze wymagają specjalnych konfiguracji przed wyeksportem pakietu usług:

* Jeśli chcesz wdrożyć usługę w chmurze, która używa secure sockets layer (SSL) do szyfrowania danych, [skonfiguruj aplikację](cloud-services-configure-ssl-certificate-portal.md#modify) dla SSL.
* Jeśli chcesz skonfigurować połączenia pulpitu zdalnego do wystąpień ról, [skonfiguruj role pulpitu](cloud-services-role-enable-remote-desktop-new-portal.md) zdalnego.
* Jeśli chcesz skonfigurować pełne monitorowanie usługi w chmurze, włącz diagnostykę platformy Azure dla usługi w chmurze. *Minimalne monitorowanie* (domyślny poziom monitorowania) używa liczników wydajności zebranych z systemów operacyjnych hosta dla wystąpień ról (maszyn wirtualnych). *Pełne monitorowanie* gromadzi dodatkowe metryki na podstawie danych dotyczących wydajności w wystąpieniach roli, aby umożliwić ściślejszą analizę problemów występujących podczas przetwarzania aplikacji. Aby dowiedzieć się, jak włączyć diagnostykę platformy Azure, zobacz [Włączanie diagnostyki na platformie Azure.](cloud-services-dotnet-diagnostics.md)

Aby utworzyć usługę w chmurze z wdrożeniami ról sieci web lub ról procesu [roboczego,](cloud-services-model-and-package.md#servicepackagecspkg)należy utworzyć pakiet usług .

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Jeśli zestaw SDK platformy Azure nie został zainstalowany, kliknij pozycję **Zainstaluj zestaw Azure SDK,** aby otworzyć [stronę Pobieranie platformy Azure,](https://azure.microsoft.com/downloads/)a następnie pobierz zestaw SDK dla języka, w którym wolisz opracować kod. (Będziesz miał możliwość zrobienia tego później.)
* Jeśli dowolne wystąpienia roli wymagają certyfikatu, utwórz certyfikaty. Usługi w chmurze wymagają pliku .pfx z kluczem prywatnym. Certyfikaty można przekazać na platformę Azure podczas tworzenia i wdrażania usługi w chmurze.

## <a name="create-and-deploy"></a>Tworzenie i wdrażanie
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij **pozycję Utwórz zasób > obliczeń,** a następnie przewiń w dół i kliknij pozycję **Usługa w chmurze**.

    ![Publikowanie usługi w chmurze](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. W nowym okienku **usługi w chmurze** wprowadź wartość **nazwy DNS**.
4. Utwórz nową **grupę zasobów** lub wybierz istniejącą.
5. Wybierz **lokalizację**.
6. Kliknij **pozycję Pakiet**. Spowoduje to otwarcie **okienka Przekaż pakiet.** Wypełnij wymagane pola. Jeśli którakolwiek z ról zawiera pojedyncze wystąpienie, upewnij się, że **deploy, nawet jeśli jedna lub więcej ról zawiera pojedyncze wystąpienie** jest zaznaczone.
7. Upewnij się, że wybrano **opcję Rozpocznij wdrażanie.**
8. Kliknij **przycisk OK,** które spowoduje zamknięcie **okienka Przekaż pakiet.**
9. Jeśli nie masz żadnych certyfikatów do dodania, kliknij przycisk **Utwórz**.

    ![Publikowanie usługi w chmurze](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Przekazywanie certyfikatu
Jeśli pakiet wdrażania został [skonfigurowany do używania certyfikatów,](cloud-services-configure-ssl-certificate-portal.md#modify)można przekazać certyfikat teraz.

1. Wybierz **pozycję Certyfikaty**i w okienku **Dodawanie certyfikatów** wybierz plik PFX certyfikatu SSL, a następnie podaj **hasło** certyfikatu,
2. Kliknij **pozycję Dołącz certyfikat**, a następnie kliknij przycisk **OK** w okienku **Dodawanie certyfikatów.**
3. Kliknij **pozycję Utwórz** w okienku **Usługi w chmurze.** Po osiągnięciu stanu **Gotowości** wdrożenia można przejść do następnych kroków.

    ![Publikowanie usługi w chmurze](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Weryfikacja pomyślnego wdrożenia została ukończona
1. Kliknij wystąpienie usługi w chmurze.

    Stan powinien pokazać, że usługa jest **uruchomiona**.
2. W **obszarze Podstawowe elementy**kliknij adres URL **witryny,** aby otworzyć usługę w chmurze w przeglądarce internetowej.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Następne kroki
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą w chmurze](cloud-services-how-to-manage-portal.md).
* Konfigurowanie [certyfikatów ssl](cloud-services-configure-ssl-certificate-portal.md).



