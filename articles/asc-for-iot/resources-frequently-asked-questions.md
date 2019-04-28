---
title: Często zadawane pytania dotyczące usługi Azure Security Center w wersji zapoznawczej IoT | Dokumentacja firmy Microsoft
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure Security Center dla funkcji IoT i usługi.
services: asc-for-iot
ms.service: ASCforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: cd9e77e09e2a2fd5fe1108e98da028d26fc623c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803105"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Centrum zabezpieczeń Azure dla IoT — często zadawane pytania  

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera listę często zadawanych pytań i odpowiedzi dotyczących usługi Azure Security Center (ASC) dla IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>Azure zapewnia obsługę dla zabezpieczeń IoT?

System Azure oferuje zintegrowane widok monitorowania oraz zarządzania nimi zabezpieczeń IoT jako część ogólnego rozwiązania zabezpieczeń za pomocą usługi Azure Security Center. Jeśli jesteś deweloperem aplikacji, można użyć widok IoT Hub do zarządzania bezpieczeństwo aplikacji IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Co to jest Azure unikatowe korzyści dotyczące zabezpieczeń IoT?

ASC IoT umożliwia przedsiębiorstwom rozszerzyć ich istniejący widok bezpieczeństwa cybernetycznego całe rozwiązanie IoT. Platforma Azure udostępnia widok kompleksowe rozwiązania biznesowe, dzięki czemu możesz podjąć akcje dotyczące firmy i decyzje oparte na poziomu bezpieczeństwa organizacji i zebranych danych. Razem zabezpieczeń za pomocą usługi Azure IoT, usługa Azure IoT Edge, Azure Sphere, Azure Central oraz Centrum zabezpieczeń Azure umożliwiają tworzenie rozwiązań, które mają z zabezpieczeniami, których potrzebujesz.

## <a name="who-is-asc-for-iot-made-for"></a>Kto jest ASC przewidzieć IoT? 

ASC IoT jest zintegrowana w ramach usługi Azure IoT Hub zabezpieczeń oraz zapewnia zarządzanie dla rozwiązań biznesowych codzienne operacje zabezpieczeń. ASC IoT jest również zintegrowane funkcje usługi Azure Security Center i podaj zintegrowany widok monitorowania oraz zarządzania nimi zabezpieczeń IoT jako część ogólnego rozwiązania zabezpieczeń.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Jak wygląda porównanie ASC IoT do konkursu?

Podczas gdy inne rozwiązania zapewniają zestaw funkcji, które umożliwiają klientom tworzenie własnych rozwiązań, ASC IoT udostępnia unikatowe rozwiązanie do zabezpieczeń IoT end-to-end, który zapewnia szerokie w zabezpieczenia wszystkich powiązanych zasobów platformy Azure. Platforma Azure zapewnia szybkie wdrażanie i Pełna integracja z bliźniaczych reprezentacjach modułów usługi IoT Hub łatwą integrację z istniejących narzędzi do zarządzania urządzeniami.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Czy muszę być klientem usługi Azure Security Center (ASC)?

Nie, ale jest to zalecane. Bez ASC ASC IoT odbiera dane ograniczona połączonych zasobów i udostępnia ograniczoną analizy, potencjalny obszar ataków, zagrożenia i potencjalnych ataków. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Czy muszę być klientem usługi Azure IoT?

Tak. ASC IoT opiera się na łączności usługi Azure IoT i infrastruktury.

## <a name="do-i-have-to-install-an-agent"></a>Trzeba zainstalować agenta ochrony?

Instalacja agenta na urządzeniach IoT nie jest wymagane w celu umożliwienia ASC firmy Microsoft dla IoT. Możesz wybrać jedną z następujących trzech opcji uzyskiwanie możliwości monitorowania i zarządzania, zgodnie z wybraną różnych poziomów zabezpieczeń:

1. Zainstaluj usługi ASC agenta zabezpieczeń IoT, z modyfikacjami lub bez. Ta opcja zapewnia najwyższy poziom zwiększone zabezpieczenia szczegółowych informacji o urządzeniu zachowania i dostępu.

2. Tworzenie własnego agenta i implementowanie ASC firmy Microsoft dla schematu komunikat zabezpieczeń IoT. Ta opcja umożliwia użycie Microsoft ASC IoT narzędzia analizy na podstawie swojego agenta zabezpieczeń urządzenia.

3. Nie instalacji agenta zabezpieczeń na urządzeniach IoT. Ta opcja umożliwia włączenie usługi IoT Hub monitorowania komunikacji, z funkcjami monitorowania i zarządzania ograniczonym zabezpieczeń. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>Do czego służy ASC agenta IoT?

ASC agenta IoT udostępnia pokrycia poziomu zagrożenia urządzenia dla konfiguracji urządzenia, zachowanie i dostęp (za pomocą skanowania konfiguracji), proces i łączności. ASC agenta zabezpieczeń IoT nie skanuje dane dotyczące firmy lub działania.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Gdzie mogę uzyskać ASC agenta zabezpieczeń IoT?

ASC agenta zabezpieczeń IoT jest typu open source i jest dostępny w witrynie GitHub w 32-bitowe i 64-bitowe wersje Windows i Linux: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Gdzie zainstalowane ASC agenta IoT? 

Szczegółowe informacje o wdrożeniu instalacji i agenta można znaleźć w witrynie GitHub: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Jakie są zależności i wymagania wstępne dotyczące agenta?

ASC IoT obsługuje różne platformy. Zobacz [platform obsługiwanych urządzeń](how-to-deploy-agent.md) Aby sprawdzić, pomocy technicznej dla określonych urządzeń. 

## <a name="which-data-is-collected-by-the-agent"></a>Dane, które są zbierane przez agenta?

Łączność, access, konfigurację zapory, listy procesów i punktem odniesienia systemu operacyjnego są zbierane przez agenta.

## <a name="how-much-data-will-the-agent-generate"></a>Jak dużo danych spowoduje wygenerowanie agenta

Generowanie danych agenta jest wymuszany przez urządzenia, aplikacji, typ połączenia i konfiguracja agenta klienta. Ze względu na wysoką zmiennością między urządzeniami i rozwiązań IoT zaleca się pierwszą wdrażanie agenta w laboratorium lub ustawienie do obserwowania, Dowiedz się więcej i ustawić konkretnej konfiguracji, który spełnia Twoje potrzeby, podczas pomiarów ilość wygenerowanych danych testu. Po uruchomieniu usługi ASC agenta IoT oferuje operacyjne zalecenia dotyczące optymalizowania przepływności agenta do udzielenia odpowiedzi na proces konfiguracji i dostosowania.

## <a name="how-can-i-control-my-billing"></a>Metody kontrolowania Moje rozliczenie?

ASC IoT udostępnia agenta można skonfigurować skanowania buforów danych oraz możliwość tworzenia niestandardowe alerty, które zwiększyć lub zmniejszyć ilość danych generowanych przez agenta.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Czy agent wiadomości użycie przydziału w usłudze IoT Hub?

Tak. Dane przesyłane agenta jest liczony limitu usługi IoT Hub. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Co dalej? Zainstalowano agenta i nie widzisz żadnych działań lub dzienniki

1. Sprawdź [pasuje do typu agenta wyznaczonego Platforma systemu operacyjnego urządzenia](how-to-deploy-agent.md)

1. Upewnij się, [agent działa na urządzeniu](how-to-agent-configuration.md).

2. Sprawdź [usługa została pomyślnie włączona](quickstart-onboard-iot-hub.md) do **zabezpieczeń** w usłudze IoT Hub. 

3. Upewnij się, że urządzenie jest [skonfigurowane w usłudze IoT Hub przy użyciu usługi ASC modułu IoT](quickstart-create-security-twin.md).  

Jeśli działania lub dzienniki są nadal dostępne, skontaktuj się z usługi ASC partnerem IoT w celu uzyskania dodatkowej pomocy.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co się stanie, gdy połączenie z Internetem przestaje działać?

Agent w dalszym uruchamiania i przechowywania danych, tak długo, jak działa urządzenie. Dane są przechowywane w pamięci podręcznej komunikatu o zabezpieczeń zgodnie z konfiguracją rozmiar. Gdy urządzenie przez połączenie, komunikaty zabezpieczeń wznowić wysyłanie. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Jeśli urządzenie zostanie ponownie uruchomiony, agent zabezpieczeń własnym odzyska?

Agent zabezpieczeń jest przeznaczony do automatycznie ponownie uruchom przy każdym ponownym uruchomieniu urządzenia.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Agenta mogą wpływać na wydajność urządzenia lub inne zainstalowane oprogramowanie?

Agent wykorzystuje zasoby maszyny jako żadnych innych aplikacji/proces i nie powinien przerwać urządzenia normalne działanie. Użycie zasobów na urządzenie, na którym działa agent jest sprzężona z jej instalacją i konfiguracją. Zalecamy przeprowadzenie testowania konfiguracji agenta w środowisku zawartej w wraz z współdziałanie z Twojej aplikacji Internetu rzeczy i funkcje, przed podjęciem próby wdrożenia w środowisku produkcyjnym.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Niektóre konserwacji powoduję, że na urządzeniu. Można wyłączyć agenta?

Agent nie może być wyłączona.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Czy istnieje sposób, aby sprawdzić, czy agent działa poprawnie? 

Czy agent zatrzymuje się podczas komunikowania się nie uda się wysłać komunikaty zabezpieczeń **urządzenie jest w trybie dyskretnym** generowany jest alert.

## <a name="can-i-create-my-own-alerts"></a>Można tworzyć własne alerty?

Tak. Można ustawić alert w postaci dostosowanego w ustalonej zestaw zachowań, takich jak adres IP i otwieranie portów. Zobacz [tworzyć niestandardowe alerty](quickstart-create-custom-alerts.md) Aby dowiedzieć się więcej na temat niestandardowe alerty i jak za ich. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Gdzie można zobaczyć dzienniki? Czy można dostosować dzienniki?

- Wyświetl alerty i zalecenia za pomocą połączonych obszaru roboczego usługi Log Analytics. W obszarze roboczym, należy skonfigurować rozmiar pamięci masowej i czas trwania.

- Nieprzetworzone dane od agenta zabezpieczeń również może znajdować się na Twoim koncie usługi Log Analytics. Przed zmianą konfiguracji tej opcji, należy rozważyć rozmiar, czas trwania, wymagania dotyczące magazynu i powiązanych z nimi kosztów. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Dlaczego należy dodać ASC dla IoT tożsamości modułu? Co to jest używany dla?

ASC IoT moduł jest używany w celu skonfigurowania agenta i zarządzania.


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tym, jak rozpocząć pracę z usługą ASC dla IoT, zobacz następujące artykuły:


- Przeczytaj ASC, aby uzyskać IoT [— omówienie](overview.md)
- Sprawdź [usługi wymagań wstępnych](service-prerequisites.md)
- Dowiedz się więcej na temat [wprowadzenie](getting-started.md)
- Zrozumienie [ASC alerty zabezpieczeń IoT](concept-security-alerts.md)

