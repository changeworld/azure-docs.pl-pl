---
title: Monitorowanie zabezpieczeń kontenerów w usłudze Azure Security Center
description: Dowiedz się, jak sprawdzić poziom zabezpieczeń kontenerów w usłudze Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919536"
---
# <a name="monitoring-the-security-of-your-containers"></a>Monitorowanie bezpieczeństwa kontenerów

Na tej stronie wyjaśniono, jak używać funkcji zabezpieczeń kontenera opisanych w [artykule Zabezpieczenia kontenera](container-security.md) w naszej sekcji pojęć.

Usługa Azure Security Center obejmuje następujące trzy aspekty zabezpieczeń kontenera:

- **Zarządzanie lukami** w zabezpieczeniach — jeśli korzystasz ze standardowej warstwy cenowej usługi Security Center (zobacz [ceny),](/azure/security-center/security-center-pricing)możesz skanować rejestr kontenerów platformy Azure oparty na systemie ARM za każdym razem, gdy zostanie wypchnięty nowy obraz. Skaner (zasilany przez Qualys) przedstawia ustalenia jako zalecenia Centrum zabezpieczeń.
    Aby uzyskać szczegółowe instrukcje, zobacz [Skanowanie rejestrów kontenerów w poszukiwaniu luk](#scanning-your-arm-based-container-registries-for-vulnerabilities) w zabezpieczeniach poniżej.

- **Wzmacnianie hostów platformy Docker kontenerów** — usługa Security Center wyszukuje niezarządzane kontenery hostowane na maszynach wirtualnych IaaS Linux lub innych maszynach z systemem Linux z systemem Docker i stale porównuje konfiguracje kontenerów z punktem odniesienia platformy Docker Center for Internet Security (CIS). Usługa Security Center ostrzega, jeśli kontenery nie spełniają żadnych formantów. Ciągłe monitorowanie zagrożeń bezpieczeństwa spowodowanych błędnymi konfiguracjami jest kluczowym elementem każdego programu zabezpieczeń. 
    Szczegółowe instrukcje można znaleźć w witrynie [Platformy Docker dla kontenerów](#hardening-your-containers-docker-hosts) poniżej.

- **Wzmocnienie klastrów usługi Azure Kubernetes** — usługa Security Center zawiera zalecenia dotyczące znalezienia luk w zabezpieczeniach w konfiguracji klastrów usługi Azure Kubernetes. Aby uzyskać szczegółowe informacje na temat konkretnych zaleceń, które mogą się pojawić, zobacz [zalecenia usługi Kubernetes](recommendations-reference.md#recs-containers).

- **Ochrona środowiska uruchomieniowego** — jeśli korzystasz ze standardowej warstwy cenowej usługi Security Center, otrzymasz ochronę przed zagrożeniami w czasie rzeczywistym dla środowisk konteneryzowanych. Usługa Security Center generuje alerty o podejrzanych działaniach na poziomie hosta i klastra AKS. Aby uzyskać szczegółowe informacje na temat odpowiednich alertów zabezpieczeń, które mogą się pojawić, zobacz [alerty dla klastrów usługi Azure Kubernetes](alerts-reference.md#alerts-akscluster) i [alerty dla kontenerów —](alerts-reference.md#alerts-containerhost) sekcje poziomu hosta tabeli odwołania alertów.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Skanowanie rejestrów kontenerów opartych na arm w poszukiwaniu luk w zabezpieczeniach 

1. Aby włączyć skanowanie luk w zabezpieczeniach obrazów rejestru kontenerów platformy Azure:

    1. Upewnij się, że korzystasz ze standardowej warstwy cenowej usługi Azure Security Center.

    1. Na stronie **Ustawienia & cen włącz** opcjonalny pakiet rejestrów kontenerów ![dla subskrypcji: Włączanie pakietu Rejestry kontenerów](media/monitor-container-security/enabling-container-registries-bundle.png)

        Usługa Security Center jest teraz gotowa do skanowania obrazów, które są wypychane do rejestru. 

        >[!NOTE]
        >Ta funkcja jest naliczana za obraz.


1. Aby wyzwolić skanowanie obrazu, wypchnij go do rejestru. 

    Po zakończeniu skanowania (zazwyczaj po około 10 minutach) wyniki są dostępne w zaleceniach centrum zabezpieczeń.
    

1. Aby wyświetlić wyniki, przejdź do strony **Zalecenia.** Jeśli problemy zostały znalezione, zobaczysz następujące zalecenia:

    ![Zalecenie dotyczące rozwiązywania problemów ](media/monitor-container-security/acr-finding.png)


1. Wybierz zalecenie. 
    Strona szczegółów rekomendacji zostanie otwarta z dodatkowymi informacjami. Informacje te obejmują listę rejestrów z obrazami zagrożonymi ("zasoby podlegające usterce") oraz kroki korygowania. 

1. Wybierz określony rejestr, aby wyświetlić repozytoria w nim, które mają repozytoria zagrożone.

    ![Wybieranie rejestru](media/monitor-container-security/acr-finding-select-registry.png)

    Zostanie otwarta strona szczegółów rejestru z listą repozytoriów, których dotyczy problem.

1. Wybierz określone repozytorium, aby wyświetlić znajdujące się w nim repozytoria, które mają obrazy zagrożone.

    ![Wybieranie repozytorium](media/monitor-container-security/acr-finding-select-repository.png)

    Zostanie otwarta strona szczegółów repozytorium. Wymienia obrazy wrażliwe wraz z oceną powagi ustaleń.

1. Wybierz określony obraz, aby zobaczyć luki w zabezpieczeniach.

    ![Zaznaczanie obrazów](media/monitor-container-security/acr-finding-select-image.png)

    Zostanie otwarta lista wyników dla wybranego obrazu.

    ![Lista ustaleń](media/monitor-container-security/acr-findings.png)

1. Aby dowiedzieć się więcej o znalezieniu, wybierz wyszukiwanie. 

    Zostanie otwarte okienko szczegółów wyników.

    [![Okienko szczegółów wyników](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    To okienko zawiera szczegółowy opis problemu i łącza do zasobów zewnętrznych, aby pomóc w ograniczeniu zagrożeń.

1. Wykonaj kroki opisane w sekcji korygowania tego okienka.

1. Po wykonaniu czynności wymaganych do rozwiązania problemu z zabezpieczeniami zastąp obraz w rejestrze:

    1. Wypchnij zaktualizowany obraz. Spowoduje to uruchomienie skanowania. 
    
    1. Sprawdź na stronie zalecenia zalecenie "Luki w obrazach rejestru kontenerów platformy Azure powinny zostać naprawione". 
    
        Jeśli zalecenie nadal jest wyświetlane, a obsługiwany obraz nadal pojawia się na liście obrazów zagrożonych, sprawdź ponownie kroki korygowania.

    1. Jeśli masz pewność, że zaktualizowany obraz został wypchnięty, zeskanowany i nie pojawia się już w zaleceniu, usuń "stary" obraz zagrożony z rejestru.


## <a name="hardening-your-containers-docker-hosts"></a>Wzmacnianie hostów platformy Docker kontenerów

Usługa Security Center stale monitoruje konfigurację hostów platformy Docker i generuje zalecenia dotyczące zabezpieczeń, które odzwierciedlają standardy branżowe.

Aby wyświetlić zalecenia dotyczące zabezpieczeń usługi Azure Security Center dla hostów platformy Docker kontenerów:

1. Na pasku nawigacyjnym Centrum zabezpieczeń otwórz pozycję **Oblicz & aplikacje** i wybierz kartę **Kontenery.**

1. Opcjonalnie należy filtrować listę zasobów kontenera do hostów kontenerów.

    ![Filtr zasobów kontenera](media/monitor-container-security/container-resources-filter.png)

1. Z listy komputerów hosta kontenerów wybierz jeden, aby zbadać dalej.

    ![Zalecenia dotyczące hosta kontenerów](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Zostanie **otwarta strona z informacjami o hoście kontenera** ze szczegółami hosta i listą zaleceń.

1. Z listy zaleceń wybierz zalecenie, aby zbadać dalej.

    ![Lista rekomendacji hosta kontenerów](media/monitor-container-security/container-host-rec.png)

1. Opcjonalnie przeczytaj opis, informacje, zagrożenia i kroki korygowania. 

1. Wybierz **pozycję Podejmij działanie** u dołu strony.

    [![Przycisk Działań](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Usługa Log Analytics zostanie otwarta z operacją niestandardową gotową do uruchomienia. Domyślna kwerenda niestandardowa zawiera listę wszystkich reguł, które zostały ocenione, a także wskazówki ułatwiające rozwiązanie problemów.

    [![Akcja analizy dzienników](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Dostosuj parametry zapytania i wybierz pozycję **Uruchom,** gdy masz pewność, że jest gotowy na hosta. 



## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak korzystać z funkcji zabezpieczeń kontenera usługi Security Center. 

Inne powiązane materiały można znaleźć na następujących stronach: 

- [Zalecenia centrum zabezpieczeń dla kontenerów](recommendations-reference.md#recs-containers)
- [Alerty dotyczące poziomu klastra AKS](alerts-reference.md#alerts-akscluster)
- [Alerty dotyczące poziomu hosta kontenera](alerts-reference.md#alerts-containerhost)
