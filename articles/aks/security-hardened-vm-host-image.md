---
title: Wzmacnianie zabezpieczeń w hostach maszyn wirtualnych AKS
description: Dowiedz się więcej o zabezpieczeniu zabezpieczeń w umacnianiu systemu operacyjnego hosta maszyn wirtualnych systemu AKS
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594384"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Wzmacnianie zabezpieczeń w hostach maszyn wirtualnych AKS 

Usługa Azure Kubernetes Service (AKS) to bezpieczna usługa zgodna ze standardami SOC, ISO, PCI DSS i HIPAA. W tym artykule opisano wzmocnienie zabezpieczeń stosowane do hostów maszyn wirtualnych usługi AKS. Aby uzyskać więcej informacji na temat zabezpieczeń usługi AKS, zobacz [Pojęcia zabezpieczeń dla aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

Klastry AKS są wdrażane na maszynach wirtualnych hosta, które uruchamiają system operacyjny zoptymalizowany pod kątem zabezpieczeń. Ten system operacyjny hosta jest obecnie oparty na obrazie Ubuntu 16.04.LTS z zestawem dodatkowych kroków wzmacniania zabezpieczeń (zobacz Szczegóły hartowania zabezpieczeń).   

Celem zabezpieczeń wzmocniony host OS jest zmniejszenie powierzchni ataku i umożliwić wdrażanie kontenerów w bezpieczny sposób. 

> [!Important]
> Wzmocniony zabezpieczenia systemu operacyjnego nie jest wytłoczony w standardach CIS. Chociaż istnieją nakładanie się na wskaźniki wc, celem nie jest zgodność z WNP. Celem zaostrzenia systemu operacyjnego hosta jest zbieżnie się na poziomie zabezpieczeń zgodnym z wewnętrznymi standardami zabezpieczeń hosta firmy Microsoft. 

## <a name="security-hardening-features"></a>Funkcje hartowania zabezpieczeń 

* AKS domyślnie zapewnia zoptymalizowany pod względem zabezpieczeń system operacyjny hosta. Nie ma bieżącej opcji wyboru alternatywnego systemu operacyjnego. 

* Platforma Azure stosuje codzienne poprawki (w tym poprawki zabezpieczeń) do hostów maszyn wirtualnych AKS. Niektóre z tych poprawek będą wymagały ponownego uruchomienia, podczas gdy inne nie. W razie potrzeby użytkownik jest odpowiedzialny za planowanie ponownego uruchamiania hosta maszyny wirtualnej AKS. Aby uzyskać wskazówki dotyczące automatyzacji poprawek AKS, zobacz [łatanie węzłów AKS](https://docs.microsoft.com/azure/aks/node-updates-kured).

Poniżej znajduje się podsumowanie pracy hartowania obrazu, który jest zaimplementowany w AKS-Engine do produkcji zoptymalizowanego pod względem zabezpieczeń systemu operacyjnego hosta. Prace zostały zrealizowane [w tym projekcie GitHub](https://github.com/Azure/aks-engine/projects/7).  

Usługa AKS-Engine nie promuje ani nie przestrzega żadnych określonych standardów zabezpieczeń w tej chwili, ale identyfikatory inspekcji CIS (Center for Internet Security) są udostępniane dla wygody, w stosownych przypadkach. 

## <a name="whats-configured"></a>Co jest skonfigurowane?

| Cis  | Opis inspekcji| 
|---|---|
| 1.1.1.1 |Upewnij się, że montaż systemów plików cramfs jest wyłączony|
| 1.1.1.2 |Upewnij się, że montaż systemów plików freevxfs jest wyłączony|
| 1.1.1.3 |Upewnij się, że montaż systemów plików jffs2 jest wyłączony|
| 1.1.1.4 |Upewnij się, że montaż systemów plików HFS jest wyłączony|
| 1.1.1.5 |Upewnij się, że montaż systemów plików HFS Plus jest wyłączony|
|1.4.3 |Upewnij się, że uwierzytelnianie jest wymagane w trybie pojedynczego użytkownika |
|1.7.1.2 |Upewnij się, że lokalny baner ostrzegawczy logowania jest poprawnie skonfigurowany |
|1.7.1.3 |Upewnij się, że baner z ostrzeżeniem o zdalnym logowaniu jest poprawnie skonfigurowany |
|1.7.1.5 |Upewnij się, że skonfigurowano uprawnienia dotyczące /etc/issue |
|1.7.1.6 |Upewnij się, że uprawnienia na /etc/issue.net są skonfigurowane |
|2.1.5 |Upewnij się, że limit czasu --streaming-connection-idle-time nie jest ustawiony na 0 |
|3.1.2 |Upewnij się, że wysyłanie przekierowania pakietów jest wyłączone |
|3.2.1 |Upewnij się, że pakiety kierowane źródła nie są akceptowane |
|3.2.2 |Upewnij się, że przekierowania ICMP nie są akceptowane |
|3.2.3 |Upewnij się, że bezpieczne przekierowania ICMP nie są akceptowane |
|3.2.4 |Upewnij się, że podejrzane pakiety są rejestrowane |
|3.3.1 |Upewnij się, że anonse routera IPv6 nie są akceptowane |
|3.5.1 |Upewnij się, że dccp jest wyłączony |
|3.5.2 |Upewnij się, że SCTP jest wyłączony |
|3.5.3 |Upewnij się, że RDS jest wyłączony |
|3.5.4 |Upewnij się, że TIPC jest wyłączony |
|4.2.1.2 |Upewnij się, że rejestrowanie jest skonfigurowane |
|5.1.2 |Upewnij się, że skonfigurowano uprawnienia na /etc/crontab |
|5.2.4 |Upewnij się, że przekazywanie SSH X11 jest wyłączone |
|5.2.5 |Upewnij się, że SSH MaxAuthTries jest ustawiony na 4 lub mniej |
|5.2.8 |Upewnij się, że logowanie roota SSH jest wyłączone |
|5.2.10 |Upewnij się, że SSH PermitUserEnvironment jest wyłączony |
|5.2.11 |Upewnij się, że używane są tylko zatwierdzone algorytmy MAX |
|5.2.12 |Upewnij się, że skonfigurowany jest interwał limitu czasu bezczynnego ssha |
|5.2.13 |Upewnij się, że SSH LoginGraceTime jest ustawiony na minutę lub mniej |
|5.2.15 |Upewnij się, że jest skonfigurowany baner ostrzegawczy SSH |
|5.3.1 |Upewnij się, że skonfigurowano wymagania dotyczące tworzenia haseł |
|5.4.1.1 |Upewnij się, że wygaśnięcie hasła wynosi 90 dni lub mniej |
|5.4.1.4 |Upewnij się, że nieaktywna blokada hasła trwa co najmniej 30 dni |
|5.4.4 |Upewnij się, że domyślna maska użytkownika jest 027 lub bardziej restrykcyjna |
|5.6 |Upewnij się, że dostęp do polecenia su jest ograniczony|

## <a name="additional-notes"></a>Uwagi dodatkowe
 
* Aby jeszcze bardziej zmniejszyć powierzchnię ataku, niektóre niepotrzebne sterowniki modułów jądra zostały wyłączone w os. 

* Wzmocniony zabezpieczenia systemu operacyjnego NIE jest obsługiwany poza platformą AKS. 

## <a name="next-steps"></a>Następne kroki  

Zobacz następujące artykuły, aby uzyskać więcej informacji na temat zabezpieczeń usługi AKS: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Zagadnienia dotyczące zabezpieczeń AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Najważniejsze wskazówki dotyczące aksu](https://docs.microsoft.com/azure/aks/best-practices)
