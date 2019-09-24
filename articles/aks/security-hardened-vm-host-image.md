---
title: Zabezpieczanie zabezpieczeń na hostach maszyn wirtualnych AKS
description: Dowiedz się więcej o zabezpieczaniu zabezpieczeń w systemie operacyjnym hosta maszyny wirtualnej AKS
services: container-service
author: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 5c2ecd5a53bb77193469ba1135c46d9b5fa65b2c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202978"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Zabezpieczanie zabezpieczeń na hostach maszyn wirtualnych AKS 

Usługa Azure Kubernetes Service (AKS) jest bezpieczną usługą zgodną ze standardami SOC, ISO, PCI DSS i HIPAA. W tym artykule opisano zaostrzone zabezpieczenia stosowane do AKS hostów maszyn wirtualnych. Aby uzyskać więcej informacji o zabezpieczeniach AKS, zobacz [pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

Klastry AKS są wdrażane na maszynach wirtualnych hosta, w których działa zoptymalizowany system operacyjny. Ten system operacyjny hosta jest obecnie oparty na obrazie Ubuntu 16.04. LTS z zestawem dodatkowych kroków związanych z zaostrzonymi zabezpieczeniami (zobacz szczegóły dotyczące ograniczania zabezpieczeń).   

Celem systemu operacyjnego hosta z zaostrzonymi zabezpieczeniami jest zredukowanie obszaru ataków i umożliwienie wdrożenia kontenerów w bezpieczny sposób. 

> [!Important]
> System operacyjny z ograniczeniami zabezpieczeń nie jest w wersji testowej. Chociaż są nakładane na testy porównawcze CIS, cel nie jest zgodny z modelem CIS. Celem zapewnienia funkcjonalności systemu operacyjnego hosta jest zbieżność poziomu zabezpieczeń spójnego z wewnętrznymi standardami zabezpieczeń hostów firmy Microsoft. 

## <a name="security-hardening-features"></a>Funkcje ograniczania zabezpieczeń 

* AKS zapewnia domyślnie system operacyjny hosta zoptymalizowanego pod kątem zabezpieczeń. Nie ma bieżącej opcji, aby wybrać alternatywny system operacyjny. 

* Na platformie Azure są stosowane codzienne poprawki (w tym poprawki zabezpieczeń) do AKS hostów maszyn wirtualnych. Niektóre z tych poprawek będą wymagały ponownego uruchomienia, a inne nie. Użytkownik jest odpowiedzialny za planowanie ponownych uruchomień hosta maszyn wirtualnych AKS w razie potrzeby. Aby uzyskać informacje na temat sposobu automatyzowania poprawek AKS, zobacz [poprawek węzłów AKS](https://docs.microsoft.com/en-us/azure/aks/node-updates-kured).

Poniżej znajduje się podsumowanie zadań związanych z ograniczaniem funkcjonalności obrazu, które są implementowane w aparacie AKS w celu utworzenia systemu operacyjnego hosta zoptymalizowanego pod kątem zabezpieczeń. Prace zostały zaimplementowane [w tym projekcie GitHub](https://github.com/Azure/aks-engine/projects/7).  

AKS — aparat nie promuje lub nie stosuje się do żadnego konkretnego standardu zabezpieczeń w tym momencie, ale identyfikatory inspekcji usług CIS (Center dla Internetu) są udostępniane dla wygody, jeśli ma to zastosowanie. 

## <a name="whats-configured"></a>Co jest skonfigurowane?

| SIC  | Opis inspekcji| 
|---|---|
| 1.1.1.1 |Upewnij się, że Instalowanie systemów plików cramfs jest wyłączone|
| 1.1.1.2 |Upewnij się, że Instalowanie systemów plików freevxfs jest wyłączone|
| 1.1.1.3 |Upewnij się, że Instalowanie systemów plików JFFS2 jest wyłączone|
| 1.1.1.4 |Upewnij się, że Instalowanie systemów plików HFS jest wyłączone|
| 1.1.1.5 |Upewnij się, że Instalowanie HFS i systemów plików jest wyłączone|
|1.4.3 |Upewnij się, że uwierzytelnianie jest wymagane dla trybu pojedynczego użytkownika |
|1.7.1.2 |Upewnij się, że transparent ostrzegawczy logowania lokalnego jest skonfigurowany prawidłowo |
|1.7.1.3 |Upewnij się, że transparent ostrzegawczy logowania zdalnego jest skonfigurowany prawidłowo |
|1.7.1.5 |Upewnij się, że zostały skonfigurowane uprawnienia do/etc/Issue |
|1.7.1.6 |Upewnij się, że zostały skonfigurowane uprawnienia do/etc/Issue.NET |
|ppkt |Upewnij się, że wartość--Streaming-Connection-Idle-Timeout nie jest ustawiona na 0 |
|3.1.2 |Upewnij się, że wysyłanie przekierowywanie pakietów jest wyłączone |
|3.2.1 |Upewnij się, że źródłowe pakiety routingu nie zostały zaakceptowane |
|3.2.2 |Upewnij się, że przekierowania protokołu ICMP nie są akceptowane |
|ppkt |Upewnij się, że bezpieczne przekierowania protokołu ICMP nie zostały zaakceptowane |
|3.2.4 |Upewnij się, że są zarejestrowane podejrzane pakiety |
|3.3.1 |Upewnij się, że anonse routera IPv6 nie zostały zaakceptowane |
|3.5.1 |Upewnij się, że DCCP jest wyłączone |
|3.5.2 |Upewnij się, że SCTP jest wyłączone |
|ppkt |Upewnij się, że RDS jest wyłączony |
|3.5.4 |Upewnij się, że TIPC jest wyłączone |
|4.2.1.2 |Upewnij się, że skonfigurowano rejestrowanie |
|5.1.2 |Upewnij się, że zostały skonfigurowane uprawnienia do/etc/crontab |
|ppkt |Upewnij się, że przekazywanie protokołu SSH X11 jest wyłączone |
|5.2.5 |Upewnij się, że MaxAuthTries SSH jest ustawiony na 4 lub mniej |
|5.2.8 |Upewnij się, że logowanie główne SSH jest wyłączone |
|5.2.10 |Upewnij się, że protokół SSH PermitUserEnvironment jest wyłączony |
|5.2.11 |Upewnij się, że są używane tylko zatwierdzone maksymalne algorytmy |
|5.2.12 |Upewnij się, że skonfigurowano interwał limitu czasu bezczynności SSH |
|5.2.13 |Upewnij się, że LoginGraceTime SSH jest ustawiona na jedną minutę lub mniejszą |
|5.2.15 |Upewnij się, że jest skonfigurowany transparent ostrzeżeń SSH |
|5.3.1 |Upewnij się, że skonfigurowano wymagania dotyczące tworzenia haseł |
|5.4.1.1 |Upewnij się, że wygaśnięcie hasła wynosi 90 dni lub mniej |
|5.4.1.4 |Upewnij się, że blokada hasła nieaktywnego to 30 dni lub mniej |
|5.4.4 |Upewnij się, że domyślny maska umask użytkownika jest 027 lub bardziej restrykcyjny |
|5.6 |Zapewnianie dostępu do polecenia su jest ograniczone|

## <a name="additional-notes"></a>Dodatkowe uwagi
 
* Aby jeszcze bardziej ograniczyć obszar narażony na ataki, niektóre zbędne sterowniki modułów jądra zostały wyłączone w systemie operacyjnym. 

* System operacyjny z ograniczeniami zabezpieczeń nie jest obsługiwany poza platformą AKS. 

## <a name="next-steps"></a>Następne kroki  

Więcej informacji na temat zabezpieczeń AKS można znaleźć w następujących artykułach: 

[Usługa Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Zagadnienia dotyczące zabezpieczeń AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Najlepsze rozwiązania AKS](https://docs.microsoft.com/azure/aks/best-practices)
