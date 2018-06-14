---
title: Rozwiązywanie problemów z wdrażaniem OpenShift na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z OpenShift wdrożenia na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2017
ms.locfileid: "24139454"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Rozwiązywanie problemów z wdrażaniem OpenShift na platformie Azure

Jeśli klaster OpenShift nie pomyślnie wdrożone, spróbuj rozwiązać problem zadania, aby zawęzić problem. Wyświetl stan wdrożenia i porównać poniżej kod zakończenia:

- 3 kod zakończenia: Red Hat subskrypcji nazwa użytkownika / hasło lub identyfikator organizacji / klucz aktywacji jest niepoprawny
- 4 kod zakończenia: Your Red Hat puli identyfikator jest nieprawidłowy lub nie dostępnych żadnych uprawnień
- 5 kod zakończenia: nie można aprowizować woluminu puli elastycznej Docker
- 6 kod zakończenia: Instalacja OpenShift klastra nie powiodła się
- 7 kod zakończenia: instalacji klastra OpenShift zakończyło się pomyślnie, ale dostawca rozwiązań w chmurze Azure konfiguracji nie powiodło się - config głównego na węzeł główny problem
- 8 kod zakończenia: instalacji klastra OpenShift zakończyło się pomyślnie, ale dostawca rozwiązań w chmurze Azure konfiguracji nie powiodło się - config węzła na problem z węzła głównego
- 9 Kod zakończenia: instalacji klastra OpenShift zakończyło się pomyślnie, ale dostawca rozwiązań w chmurze Azure konfiguracji nie powiodło się - config węzła na problem Infra lub węzła aplikacji
- 10 kod zakończenia: instalacji klastra OpenShift zakończyło się pomyślnie, ale konfiguracja Azure Cloud Solution Provider nie powiodło się — Korygowanie wzorca węzłów lub nie można ustawić jako unschedulable wzorca
- 11 kod zakończenia: nie można uruchomić metryk do wdrożenia
- 12 kod zakończenia: rejestrowanie wdrażanie nie powiodło się

Kody zakończenia 7-10 OpenShift klastra został zainstalowany, ale nie Konfiguracja Azure Cloud Solution Provider. Możesz SSH do węzła głównego (OpenShift pochodzenia) lub węzeł bastionu (OpenShift kontenera platformy) i z występują SSH do każdego węzła klastra, aby rozwiązać problemy z.

Typowe przyczyny błędów z kodów zakończenia 7-9 jest nazwy głównej usługi nie miał odpowiednie uprawnienia do subskrypcji lub grupy zasobów. Jeśli jest to problem, przypisz odpowiednie uprawnienia i ręcznie uruchom ponownie skrypt, który uległ awarii i wszystkie kolejne skrypty.

Pamiętaj ponownie uruchomić usługę, która nie powiodło się (na przykład systemctl ponowne uruchomienie atomic-openshift-node.service) przed wykonaniem skryptów ponownie.

Dla dalszego rozwiązywania problemów, SSH na węzeł główny na porcie 2200 (źródło) lub węzeł bastionu na port 22 (kontenera Platform). Muszą znajdować się w katalogu głównego (sudo su-), a następnie przejdź do następującego katalogu: /var/lib/waagent/custom-script/download.

W tym miejscu wyświetlić foldery o nazwie "0" i "1". W każdym z tych folderów Zobacz dwa pliki, "stderr" i "stdout." Przejrzyj te pliki, aby określić, w którym wystąpił błąd.
