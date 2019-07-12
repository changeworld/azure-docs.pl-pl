---
title: Omówienie rozliczania za usługę Azure CDN | Dokumentacja firmy Microsoft
description: Często zadawane pytania opisano, jak działa rozliczanie usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: 2fd3d2f8fbc98d8c7b19cbcc365748cc088d76fd
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594097"
---
# <a name="understanding-azure-cdn-billing"></a>Omówienie rozliczania za usługę Azure CDN

Często zadawane pytania opisujący strukturę rozliczeń dla zawartości hostowanej przez Azure zawartości Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Co to jest region płatności?
Region płatności jest obszar geograficzny używany do określenia, w jakiej stawki jest rozliczany według za dostarczanie obiektów z usługi Azure CDN. Bieżącej strefy rozliczeń i ich regionów są następujące:

- Strefa 1: Ameryka Północna, Europa, Bliski Wschód i Afryka

- Strefa 2: Azja i Pacyfik (włącznie z Japonią)

- Strefa 3: Ameryka Południowa

- Strefa 4: Australia i Nowa Zelandia

- Strefa 5: Indie

Aby uzyskać informacji na temat regionów punkt o obecności (POP), zobacz [lokalizacje POP usługi Azure CDN według regionu](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Na przykład punkt obecności znajdujący się w Meksyku znajduje się w regionie Ameryki Północnej i w związku z tym znajduje się w strefie 1. 

Aby uzyskać informacje o cenach usługi Azure CDN, zobacz [cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Jak są obliczane opłaty dostarczania według regionu?
Region płatności wysokiej dostępności treści Azure opiera się na lokalizację na serwerze źródłowym, dostarczanie zawartości do użytkownika końcowego. Miejsce docelowe (fizyczną lokalizację) klienta nie jest uważany za region płatności.

Na przykład jeśli użytkownik znajduje się w Meksyku generuje żądanie, to żądanie zostanie obsłużone przez serwer, z siedzibą na terenie Stanów Zjednoczonych w menu Podręcznym ze względu na warunki komunikacji równorzędnej lub ruch regionie rozliczeń będzie Stanów Zjednoczonych.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co to jest transakcja płatne usługi Azure CDN?
Każde żądanie HTTP (S), które kończy się na sieć CDN jest płatne zdarzenie, które obejmuje wszystkie typy odpowiedzi: powodzenie, Niepowodzenie lub innego. Jednak różne odpowiedzi może generują różnego ruchu. Na przykład *304 niezmodyfikowane* i innych odpowiedzi tylko nagłówki generowania niewielkim ruchu, ponieważ są one małą odpowiedzi nagłówka; podobnie, odpowiedzi na błędy (na przykład *404 Nie znaleziono*) są płatne ale pociągnąć za sobą niewielkim kosztem ze względu na ładunku niewielki rozmiar odpowiedzi.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jakie koszty platformy Azure są skojarzone z użyciem usługi Azure CDN?
Za pomocą usługi Azure CDN wiąże się też niektóre opłaty za użycie usług wykorzystanych jako punkt początkowy dla obiektów. Te koszty są zazwyczaj ułamku całkowity koszt użytkowania usługi CDN.

Jeśli używasz usługi Azure Blob storage jako punkt początkowy dla zawartości, również naliczone następujące opłaty za magazyn do wypełnienia pamięci podręcznej:

- Używane rzeczywiste GB: Rzeczywisty magazyn obiektów źródłowych.

- Transfery w GB: Ilość danych przesyłanych do wypełnienia podręcznych sieci CDN.

- Transakcje: Zgodnie z potrzebami, aby zapełnić pamięć podręczną.

Aby uzyskać więcej informacji o rozliczeniach usługi Azure Storage, zobacz [opis rozliczeń platformy Azure Storage — przepustowość, transakcje i pojemność](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Jeśli używasz *świadczenia usług hostowanych*, zostaną naliczone opłaty w następujący sposób:

- Godziny obliczeniowe platformy Azure: Wystąpienia obliczeniowe, które działają jako punkt początkowy.

- Transfer obliczeniowych platformy Azure: Przesyła dane z wystąpieniami obliczeniowymi w celu wypełnienia pamięci podręcznej usługi Azure CDN.

Jeśli Twój klient używa żądania zakresu bajtów (niezależnie od źródła usługi), obowiązują następujące zastrzeżenia:

- A *żądania zakresu bajtów* jest rozliczana transakcji w sieci CDN. Kiedy klient wysyła żądania zakresu bajtów, to żądanie jest dla podzbioru (zakres) obiektu. Usługa CDN odpowiada za pomocą tylko częściowe część zawartości, które są wymagane. Częściowa odpowiedź jest rozliczana transakcji i ilość transferu jest ograniczony do rozmiaru zakresu odpowiedzi (łącznie z nagłówkami).

- Po odebraniu żądania tylko część obiektu (przez określenie nagłówka zakresu bajtów) w sieci CDN w swojej pamięci podręcznej może pobrać cały obiekt. W rezultacie nawet jeśli będzie rozliczana transakcji z usługi CDN dla odpowiedzi częściowej, płatnych transakcji ze źródła może obejmować pełny rozmiar obiektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Jak dużo działań transferu występuje do obsługi pamięci podręcznej?
Każdorazowo POP sieci CDN konieczne wypełnienie pamięci podręcznej, kieruje żądanie do początkowego obiektu pamięci podręcznej. W rezultacie pochodzenia wiąże się z płatnych transakcji na każdym Chybienie pamięci podręcznej. Liczba chybień pamięci podręcznej zależy od kilku czynników:

- Podlega buforowaniu, jak jest zawartość: Jeśli zawartość ma wysoki czas wygaśnięcia (time-to-live) / wygaśnięcia wartości i jest często używane, więc pozostaje on popularne w pamięci podręcznej, a następnie większość obciążenia jest obsługiwane przez usługę CDN. Typowe dobre Współczynnik trafień pamięci podręcznej jest dobrze, ponad 90%, co oznacza, że mniej niż 10% żądań klientów musi powrócić do źródła, albo Chybienie pamięci podręcznej lub w obiekcie odświeżania.

- Ilu węzłów należy załadować obiektu: Każdym węzłem ładuje obiekt ze źródła, ponosi płatnych transakcji. W rezultacie zawartość globalnych (dostępny od więcej węzłów) powoduje transakcji do rozliczenia więcej.

- Wpływ na czas wygaśnięcia: Wyższa wartość TTL dla obiektu oznacza, że ma zostać pobrane ze źródła rzadziej. Ponadto oznacza to, że klientów, takich jak przeglądarki, można w pamięci podręcznej dłużej, obiektu, który może zmniejszyć transakcji usługi CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Jak najbardziej efektywnie zarządzać koszty?
Ustaw najdłuższy czas wygaśnięcia jest możliwe dla zawartości. 
