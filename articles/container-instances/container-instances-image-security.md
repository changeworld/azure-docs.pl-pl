---
title: Zagadnienia dotyczące zabezpieczeń usługi Azure Container Instances
description: Zalecenia dotyczące bezpiecznego obrazów i wpisów tajnych dla usługi Azure Container Instances, Ogólne zagadnienia dotyczące zabezpieczeń i dla dowolnej platformy kontenera
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943999"
---
# <a name="security-considerations-for-azure-container-instances"></a>Zagadnienia dotyczące zabezpieczeń dla usługi Azure Container Instances

W tym artykule przedstawiono zagadnienia dotyczące zabezpieczeń dotyczące korzystania z usługi Azure Container Instances można uruchamiać aplikacje kontenera. Tematy obejmują:

> [!div class="checklist"]
> * **Zalecenia dotyczące zabezpieczeń** zarządzania obrazami i wpisów tajnych dla usługi Azure Container Instances
> * **Zagadnienia dotyczące ekosystemu kontenera** w całym cyklu życia kontenera dla dowolnej platformy kontenera

## <a name="security-recommendations-for-azure-container-instances"></a>Zalecenia dotyczące zabezpieczeń dla usługi Azure Container Instances

### <a name="use-a-private-registry"></a>Skorzysta z prywatnego rejestru

Kontenery są kompilowane na podstawie obrazów przechowywanych w co najmniej jednym repozytorium. Te repozytoria mogą należeć do rejestru publicznego, takie jak [usługi Docker Hub](https://hub.docker.com), lub do prywatnego rejestru. Przykładem rejestru prywatnego jest rejestr [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), który można zainstalować w środowisku lokalnym lub w wirtualnej chmurze prywatnej. Można również użyć oparte na chmurze usługi prywatnych rejestrów kontenerów, w tym [usługi Azure Container Registry](../container-registry/container-registry-intro.md). 

Publicznie dostępny obraz kontenera nie gwarantuje bezpieczeństwa. Obrazy kontenera składają się z wielu warstw oprogramowania, a każda warstwa oprogramowania może mieć luki w zabezpieczeniach. Aby zmniejszyć zagrożenie atakami, powinny przechowywać i pobierać obrazy przy użyciu rejestru prywatnego, takiego jak usługa Azure Container Registry lub Docker Trusted Registry. Tylko oferuje zarządzany rejestr prywatny, usługi Azure Container Registry obsługuje [uwierzytelnianie oparte na jednostce usługi](../container-registry/container-registry-authentication.md) za pośrednictwem usługi Azure Active Directory dla podstawowych przepływów uwierzytelniania. To uwierzytelnianie obejmuje opartej na rolach dostęp tylko do odczytu (ściągać), zapis (push) i uprawnienia właściciela.

### <a name="monitor-and-scan-container-images"></a>Monitorowanie i skanowanie obrazów kontenerów

Zabezpieczenia i monitorowania takich jak [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) i [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) są dostępne za pośrednictwem portalu Azure Marketplace. Można ich użyć do skanowania obrazów kontenerów w rejestrze prywatnym oraz identyfikowania potencjalnych luk w zabezpieczeniach. Należy zrozumieć głębię skanowanie, aby zapewnić różne rozwiązania. 

### <a name="protect-credentials"></a>Ochrona poświadczeń

Kontenery może zostać rozłożona między wiele klastrów i regiony platformy Azure. Dlatego należy zabezpieczyć poświadczenia są wymagane dla nazwy logowania lub dostęp do interfejsu API, takie jak hasła lub tokenów. Upewnij się, że tylko uprzywilejowane użytkownicy mają dostęp do tych kontenerów przesyłanych i magazynowanych. Spis wszystkich wpisów tajnych poświadczeń, a następnie wymagają deweloperzy mogą używać pojawiających się narzędzia do zarządzania wpisami tajnymi, które są przeznaczone dla platform kontenerowych.  Upewnij się, że Twoje rozwiązanie zawiera szyfrowanymi bazami danych, szyfrowania TLS do wpisów tajnych danych przesyłania i najniższych [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md). [Usługa Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączenia i hasła) dla konteneryzowanych aplikacji. Ponieważ te dane są wrażliwe i krytyczne dla działania, bezpieczny dostęp do Twojego klucza magazyny tak, aby tylko autoryzowane aplikacje i użytkownicy mogą uzyskiwać do nich dostęp.

## <a name="considerations-for-the-container-ecosystem"></a>Zagadnienia dotyczące ekosystemu kontenera

Następujące środki bezpieczeństwa, zaimplementowana prawidłowo i efektywnie, zarządzane mogą pomóc w bezpieczny i ochrona ekosystemu usługi kontenera. Te środki są stosowane w całym cyklu życia kontenera, od projektowania do wdrożenia produkcyjnego i szeroką gamę platform koordynatorów kontenerów, hosty i. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Użyj Zarządzanie lukami w zabezpieczeniach w ramach cyklu wytwarzania oprogramowania kontenera 

Za pomocą zarządzania skuteczne luk w zabezpieczeniach podczas cyklu tworzenia oprogramowania w kontenerze, możesz zwiększyć kolizję, możesz zidentyfikować i rozwiązać problemy dotyczące zabezpieczeń, zanim staną się bardziej poważny problem. 

### <a name="scan-for-vulnerabilities"></a>Skanowanie w poszukiwaniu luk w zabezpieczeniach 

Nowych luk w zabezpieczeniach są odnajdywane cały czas, dlatego skanowanie w poszukiwaniu i identyfikowanie luk w zabezpieczeniach jest ciągły proces. Zestawowi wyszukiwaniu luk w zabezpieczeniach w całym cyklu życia kontenera:

* Jako ostatecznego wyboru w potoku tworzenia aplikacji należy przeprowadzić skanowanie luk w zabezpieczeniach kontenerów, przed wypchnięciem obrazy do publicznego lub prywatnego rejestru. 
* Przejdź do skanowania obrazów kontenerów w rejestrze, aby zidentyfikować wszelkie wady jakiś sposób zostały pominięte podczas tworzenia i rozwiązać wszelkie nowo odkrytych luk w zabezpieczeniach, które mogą istnieć w kodzie używanym w obrazów kontenerów.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapy obrazu luki w zabezpieczeniach do uruchamiania kontenerów 

Musisz mieć oznacza luk w zabezpieczeniach mapowania identyfikowane w obrazów kontenera do uruchamiania kontenerów, dzięki czemu problemy z zabezpieczeniami można zminimalizować lub rozwiązane.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Upewnij się, że tylko obrazy zatwierdzone są używane w danym środowisku 

Istnieje wystarczająca ilość zmian i zmienności należący do ekosystemu kontenera, bez udzielania praw nieznany także z kontenerami. Zezwala na tylko zatwierdzonego kontenera obrazów. Mieć narzędzi i procesów w celu monitorowania i uniemożliwić korzystanie z obrazów kontenerów niezatwierdzonych. 

Skutecznym sposobem zmniejszyć obszar narażony na ataki co uniemożliwia wprowadzanie błędów krytycznych przez deweloperów jest sterowanie przepływem obrazów kontenera do swojego środowiska projektowego. Na przykład może być oficjalnie jednym dystrybucję systemu Linux jako obraz podstawowy, zalecane jest zwarte (Alpine lub CoreOS zamiast Ubuntu), aby zminimalizować narażony na wypadek potencjalnych ataków. 

Obraz podpisywania lub odcisków może zapewnić łańcuch nadzoru, który można zweryfikować integralności kontenerów. Na przykład usługi Azure Container Registry obsługuje platformy Docker [zawartości zaufania](https://docs.docker.com/engine/security/trust/content_trust) modelu, który umożliwia wydawców obrazów się obrazy, które zostaną wypchnięte do rejestru, a obraz w konsumentach napisanych ściąganie tylko podpisane obrazów.

### <a name="permit-only-approved-registries"></a>Zezwalanie tylko na zatwierdzonych rejestrów 

Zapewnienie, że środowisko używa tylko obrazy zatwierdzone przez rozszerzenie jest tylko używania rejestry kontenerów zatwierdzone. Wymaganie użycia rejestry kontenerów zatwierdzonych redukuje narażenie na ryzyko, ograniczając ryzyko wprowadzenia nieznany luk w zabezpieczeniach lub problemy z zabezpieczeniami. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Integralność obrazów w całym cyklu życia 

Częścią zarządzania zabezpieczeniami w całym cyklu życia kontenera jest integralność obrazów kontenera w rejestrze i jak są one zmieniane ani wdrożone do środowiska produkcyjnego. 

* Nie wolno obrazy z lukami w zabezpieczeniach, nawet drobnymi, do uruchamiania w środowisku produkcyjnym. W idealnym przypadku wszystkie obrazy wdrożone w środowisku produkcyjnym powinny być zapisane w rejestrze prywatnym dostępny wybierz kilka. Zachowaj liczbę obrazów produkcyjnych jest mała, aby upewnić się, że mogą oni efektywnie zarządzać.

* Ponieważ trudno jest wskazać źródło oprogramowania z publicznie dostępny obraz kontenera, kompilowanie obrazów na podstawie źródła w celu zidentyfikowania źródła warstwy. Gdy w samodzielnie kompilowanym obrazie kontenera pojawi się luka w zabezpieczeniach, klienci będą mogli szybciej znaleźć ścieżkę do rozwiązania. Przy użyciu obrazu publicznego Klienci musieliby znaleźć katalogu głównego obrazu, aby go naprawić, lub uzyskać inny zabezpieczony obraz od wydawcy. 

* Dokładnie zeskanowany obraz wdrożony w środowisku produkcyjnym nie jest gwarantowana powinny być zawsze aktualne, okres istnienia aplikacji. Luki w zabezpieczeniach można zgłaszać dla warstw obrazu, które nie były wcześniej znane lub pojawiły się po wdrożeniu produkcyjnym. 

  Przeprowadzaj okresowe inspekcje obrazów wdrożonych w środowisku produkcyjnym do identyfikowania obrazów, które są nieaktualne lub nie zostały zaktualizowane w chwilę. Możesz użyć metody wdrożenie niebieski zielony i mechanizmów uaktualniania stopniowego Aby aktualizować obrazy kontenerów bez przestojów. Skanowanie obrazów za pomocą narzędzi opisanych w poprzedniej sekcji. 

* Potok ciągłej integracji (CI) za pomocą zabezpieczenia zintegrowane skanowanie, aby skompilować obrazy bezpieczny i odesłać je do rejestru prywatnego. Skanowanie luk w zabezpieczeniach wbudowane w rozwiązaniu CI gwarantuje, że obrazy, które przejdą wszystkie testy, zostaną wypchnięte do rejestru prywatnego, z poziomu którego odbywa się wdrażanie obciążeń produkcyjnych. 

  Błąd potoku CI zapewnia, że narażony obrazy nie zostaną wypchnięte do rejestru prywatnego, który jest używany dla wdrożeń produkcyjnych. Możliwe jest również Automatyzowanie skanowania w przypadku znacznej liczby obrazów zabezpieczeń obrazów. W przeciwnym razie proces ręcznego przeprowadzania inspekcji obrazów w celu wyszukania luk w zabezpieczeniach może być niezwykle żmudny i narażony na błędy. 

### <a name="enforce-least-privileges-in-runtime"></a>Wymuszanie co najmniej uprawnienia w środowisku uruchomieniowym 

Pojęcie minimalnych uprawnień jest najlepszym rozwiązaniem zabezpieczeń podstawowych i ma zastosowanie również do kontenerów. Gdy luki zazwyczaj daje osobie atakującej uzyskanie dostępu i uprawnień takie same ze złamanymi zabezpieczeniami aplikacji lub procesu. Zagwarantowanie, że kontenery pracują z najniższymi uprawnieniami, i dostępu, które trzeba wykonać zadanie redukuje narażenie na ryzyko. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Zmniejsz obszar narażony na ataki kontenera przez usunięcie niepotrzebnych uprawnień 

Można także zminimalizować potencjalny obszar ataków, usuwając wszystkie nieużywane i niepotrzebne procesy lub uprawnienia z kontener środowiska uruchomieniowego. Uprzywilejowane kontenery są uruchamiane jako użytkownik główny. Jeśli złośliwy użytkownik lub Obciążenie specjalne w kontenerze uprzywilejowanego, kontener następnie działa jako główny w tym systemie.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Lista dozwolonych plików i plików wykonywalnych, które kontenera będzie mógł uzyskać dostęp, lub uruchomić 

Zmniejszenie liczby zmiennych lub elementy nieznane pomaga utrzymać stabilne i niezawodne środowisko. Ograniczanie kontenerów, dzięki czemu mogą uzyskiwać dostęp do lub Uruchom tylko Weryfikacja wstępnego zatwierdzenia lub na liście dozwolonych plików i plików wykonywalnych jest sprawdzonym metoda ograniczania narażenia na zagrożenia.  

Znacznie łatwiej zarządzać listą dozwolonych, gdy jest stosowana od samego początku. Lista dozwolonych jest miarą kontrolę i możliwości zarządzania dowiesz się, jakie pliki i pliki wykonywalne są wymagane do poprawnego działania aplikacji. 

Listy dozwolonych nie tylko zmniejsza możliwości zaatakowania, ale również zapewnia plan bazowy dla anomalie i zapobiec przypadki użycia "hałaśliwego sąsiada" i scenariuszach związanych z kontenerem podgrupach. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Wymuszanie segmentacji sieci na uruchamianie kontenerów  

Aby lepiej chronić kontenerów w jednej podsieci przed zagrożeniami bezpieczeństwa w innej podsieci, utrzymania segmentacji sieci (lub segmentacji nano) lub podziału między uruchomionych kontenerów. Obsługa segmentacji sieci mogą być również konieczne do korzystania z kontenerów w branżach, które są wymagane do spełnienia potwierdzenia zgodności, zawiera.  

Na przykład narzędzie partnera [Akwamaryna](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) zapewnia zautomatyzowanej procedury segmentacji nano. Akwamaryna monitoruje działania w sieci kontenera w środowisku uruchomieniowym. Określa on wszystkich połączeń sieciowych dla ruchu przychodzącego i wychodzącego do/z innych kontenerów, usługi, adresy IP i publicznej sieci internet. Segmentacji nano jest tworzony automatycznie w oparciu o monitorowanym ruchu. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorowanie kontenera działania oraz dostępu użytkownika 

Podobnie jak w przypadku dowolnego środowiska IT powinny stale monitorować działania oraz dostępu użytkownika ekosystemem kontenera w celu szybkiego identyfikowania wszelkich podejrzanych lub złośliwego działania. Platforma Azure udostępnia rozwiązań, w tym do monitorowania kontenerów:

* [Usługa Azure Monitor dla kontenerów](../azure-monitor/insights/container-insights-overview.md) do monitorowania wydajności obciążeń wdrożonych do rozwiązania Kubernetes środowisk hostowanych w usłudze Azure Kubernetes Service (AKS). Usługa Azure Monitor dla kontenerów zapewnia widoczność wydajności na zbieranie pamięci i procesora metryk z kontrolerów, węzły i kontenerów, które są dostępne w usłudze Kubernetes za pomocą interfejsu API metryki. 

* [Rozwiązanie do monitorowania kontenerów Azure](../azure-monitor/insights/containers.md) ułatwia wyświetlanie oraz zarządzanie nimi inne platformy Docker i Windows hostach kontenerów w jednej lokalizacji. Na przykład:

  * Wyświetl szczegółowe informacje inspekcji, które zawiera polecenia używany z kontenerami. 
  * Rozwiązywanie problemów z kontenerów, wyświetlania i przeszukiwania scentralizowanych dzienników bez konieczności zdalnie wyświetlić hostów platformy Docker lub Windows.  
  * Znajdź kontenerów, które mogą być hałas i korzystanie z nich nadmiar zasobów na hoście.
  * Wyświetl scentralizowane procesora CPU, pamięci, magazynu i informacje użycia i wydajności sieci dla kontenerów.  

  Rozwiązanie obsługuje koordynatorów kontenerów, w tym Docker Swarm, DC/OS, niezarządzanych Kubernetes, usługi Service Fabric i Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorowanie działań związanych z zasobów kontenera 

Monitoruj swoją aktywność zasobów, takich jak pliki, sieci i innych zasobów, uzyskujących dostęp do kontenerów. Monitorowanie aktywności zasobów i zużycia przydaje się do monitorowania wydajności i ze względów bezpieczeństwa. 

[Usługa Azure Monitor](../azure-monitor/overview.md) zapewnia podstawowe funkcje monitorowania usług systemu Azure, umożliwiając zbierania metryk, dzienników aktywności i dzienników diagnostycznych. Na przykład dziennik aktywności informuje o utworzeniu lub zmodyfikowaniu nowych zasobów. 

Są dostępne metryki, które dostarczają statystyki wydajności dla różnych zasobów, a nawet systemu operacyjnego w maszynie wirtualnej. Można wyświetlić te dane za pomocą jednego z eksploratorów w witrynie Azure Portal i utworzyć alerty na podstawie tych metryk. Usługa Azure Monitor udostępnia metryki najszybszy potoku (5 minut do 1 minuty), dlatego powinien być używany czas ma istotne znaczenie alertów i powiadomień. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Zaloguj się kontenera dostęp administratora do przeprowadzania inspekcji 

Obsługa dziennika inspekcji dokładne dostęp administracyjny do ekosystemu kontenerów, rejestr kontenerów i obrazów kontenerów. Te dzienniki mogą być wymagane na potrzeby inspekcji i będą przydatne jako dowód śledczej po wszystkimi zdarzeniami zabezpieczeń. Możesz użyć [rozwiązanie do monitorowania kontenerów Azure](../azure-monitor/insights/containers.md) do osiągnięcia tego celu. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o zarządzaniu kontenera luk w zabezpieczeniach dzięki rozwiązaniom [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) i [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Dowiedz się więcej o [zabezpieczenia kontenerów na platformie Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).