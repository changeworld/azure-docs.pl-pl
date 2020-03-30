---
title: Omówienie diagnostyki usługi Kubernetes (AKS) platformy Azure
description: Dowiedz się więcej o samodiagnozowaniu klastrów w usłudze Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126611"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Omówienie diagnostyki usługi Azure Kubernetes (wersja zapoznawcza)

Rozwiązywanie problemów z klastrem usługi Azure Kubernetes (AKS) jest ważną częścią obsługi klastra, zwłaszcza jeśli w klastrze są uruchomione obciążenia o znaczeniu krytycznym. Diagnostyka AKS to inteligentne środowisko samodiagnostyki, które pomaga identyfikować i rozwiązywać problemy w klastrze. Diagnostyka AKS jest natywna dla chmury i można jej używać bez dodatkowych kosztów konfiguracji lub rozliczeń.

Ta funkcja jest teraz w publicznej wersji zapoznawczej.

## <a name="open-aks-diagnostics"></a>Otwórz diagnostykę AKS

Aby uzyskać dostęp do diagnostyki AKS:

- Przejdź do klastra kubernetes w [witrynie Azure portal](https://portal.azure.com).
- Kliknij przycisk **Diagnozuj i rozwiązuj problemy** w lewej nawigacji, która otwiera diagnostykę AKS.
- Wybierz kategorię, która najlepiej opisuje problem klastra przy użyciu słów kluczowych na kafelku strony głównej, lub wpisz słowo kluczowe, które najlepiej opisuje problem na pasku wyszukiwania, na przykład _Problemy z węzłem klastra_.

![Strona główna](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Wyświetlanie raportu diagnostycznego

Po kliknięciu kategorii można wyświetlić raport diagnostyczny specyficzny dla klastra. Raport diagnostyczny inteligentnie wywołuje, jeśli istnieje jakikolwiek problem w klastrze z ikonami stanu. Możesz przejść do szczegółów każdego tematu, klikając **więcej informacji,** aby zobaczyć szczegółowy opis problemu, zalecane akcje, łącza do przydatnych dokumentów, powiązane metryki i dane rejestrowania. Raporty diagnostyczne są generowane inteligentnie na podstawie bieżącego stanu klastra po uruchomieniu różnych kontroli. Raporty diagnostyczne mogą być przydatnym narzędziem do wskazywania problemu klastra i znajdowania kolejnych kroków, aby rozwiązać problem.

![Raport diagnostyczny](./media/concepts-diagnostics/diagnostic-report.png)

![Rozszerzony raport diagnostyczny](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Szczegółowe informacje o klastrze

Następujące kontrole diagnostyczne są dostępne w **usłudze Cluster Insights**.

### <a name="cluster-node-issues"></a>Problemy z węzłem klastra

Problemy z węzłem klastra sprawdza problemy związane z węzłami, które mogą powodować nieoczekiwane zachowanie klastra.

- Problemy z gotowością węzła
- Awarie węzłów
- Niewystarczające zasoby
- Brak konfiguracji ip węzła
- Awarie CNI węzła
- Nie znaleziono węzła
- Wyłączenie zasilania węzła
- Błąd uwierzytelniania węzła
- Węzeł kube-proxy przestarzały

### <a name="create-read-update--delete-operations"></a>Operacje tworzenia, odczytu, aktualizowania & usuwania

CRUD Operations sprawdza, czy wszystkie operacje CRUD, które mogą powodować problemy w klastrze.

- Błąd operacji usuwania podsieci w użyciu
- Błąd operacji usuwania sieciowej grupy zabezpieczeń
- Błąd operacji usuwania tabeli marszruty w użyciu
- Błąd inicjowania obsługi administracyjnej zasobów, do których istnieje odwołanie
- Błąd operacji usuwania publicznego adresu IP
- Niepowodzenie wdrożenia z powodu przydziału wdrożenia
- Błąd operacji spowodowany zasadami organizacji
- Brak rejestracji subskrypcji
- Błąd inicjowania obsługi administracyjnej rozszerzenia maszyny Wirtualnej
- Pojemność podsieci
- Przekroczony błąd przydziału

### <a name="identity-and-security-management"></a>Zarządzanie tożsamościami i zabezpieczeniami

Usługa Identity and Security Management wykrywa błędy uwierzytelniania i autoryzacji, które mogą uniemożliwiać komunikację z klastrem.

- Błędy autoryzacji węzła
- 401 błędów
- Błędy 403

## <a name="next-steps"></a>Następne kroki

Zbieranie dzienników ułatwiające dalsze rozwiązywanie problemów z klastrem przy użyciu [programu AKS Periscope](https://aka.ms/aksperiscope).

Zamieść swoje pytania lub opinie na [UserVoice,](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) dodając "[Diag]" w tytule.
