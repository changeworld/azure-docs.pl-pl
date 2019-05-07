---
title: Nawiązać połączenie z systemem Windows 10 lub Windows 7 — Azure Windows wirtualnego pulpitu (wersja zapoznawcza)
description: Jak połączyć się z Windows wirtualnego pulpitu (wersja zapoznawcza) z systemem Windows 10 lub Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145985"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Łączenie z systemem Windows 10 lub Windows 7

> Dotyczy Windows 7 i Windows 10.

Dostępny jest dostępny do pobrania klient, który zapewnia dostęp do zasobów Windows wirtualnego pulpitu (wersja zapoznawcza) z urządzeń z systemem Windows 7 i Windows 10.

> [!IMPORTANT]
> Nie używaj **programów RemoteApp i połączenia pulpitu (RADC)** lub **Podłączanie pulpitu zdalnego (MSTSC)** można uzyskać dostęp do zasobów Windows pulpitu wirtualnego, ponieważ Windows pulpitu wirtualnego nie obsługuje albo klient.

## <a name="install-the-client"></a>Instalowanie klienta

[Pobierz](https://go.microsoft.com/fwlink/?linkid=2068602) i instalowanie klienta na komputerze lokalnym. Instalacja wymaga uprawnień administratora.

## <a name="subscribe-to-a-feed"></a>Subskrybować źródła danych

Pobierz listę zarządzane zasoby dostępne dla Ciebie, Subskrybuj źródło danych podane przez administratora. Subskrybowanie udostępnia zasoby na komputerze lokalnym.

Aby subskrybować źródła danych:

1. Uruchom klienta z listę wszystkich aplikacji, poszukaj dla **pulpitu zdalnego**.
1. Wybierz **Subskrybuj** na stronie głównej do połączenia z usługą i pobierania zasobów.
1. **Zaloguj się** przy użyciu konta użytkownika po wyświetleniu monitu.

Po pomyślnym uwierzytelnieniu powinny teraz wyświetlić listę dostępnych zasobów.

Zasoby można uruchomić za pomocą jednej z dwóch metod.

- Strona główna klienta kliknij dwukrotnie zasób, aby go uruchomić.
- Uruchom zasobu, tak jak zwykle innych aplikacji w Start Menu.
  - Możesz również wyszukać aplikacje na pasku wyszukiwania.

Gdy subskrybować źródła danych, zawartość kanału informacyjnego jest aktualizowane automatycznie w regularnych odstępach czasu. Zasoby mogą można dodać, zmienić ani usunąć zależności od zmian wprowadzonych przez administratora.

## <a name="view-the-details-of-a-feed"></a>Wyświetl szczegóły źródła danych

Po subskrypcji, możesz wyświetlić dodatkowe informacje o źródle danych, uzyskując dostęp do panelu szczegółów.

1. Ze strony głównego klienta, wybierz przycisk wielokropka (**...** ) po prawej stronie nazwy źródła danych.
1. Z menu rozwijanego wybierz **szczegóły**.
1. Panel szczegółów pojawia się po prawej stronie klienta.

Panel szczegółów zawierają użyteczne informacje o źródle danych:

- Adres URL i nazwa użytkownika służąca do subskrybowania
- Liczba aplikacji i komputerów stacjonarnych
- Data/godzina ostatniej aktualizacji
- Stan ostatniej aktualizacji

Jeśli to konieczne, można uruchomić ręcznej aktualizacji, wybierając na **teraz zaktualizować**.

## <a name="unsubscribe-from-a-feed"></a>Anuluj subskrypcję kanału informacyjnego

W tej sekcji nauczą Cię, jak zrezygnować z kanału informacyjnego. Subskrypcję można anulować ponownej subskrypcji za pomocą innego konta lub usunąć swoje zasoby z systemu.

1. Ze strony głównego klienta, wybierz przycisk wielokropka (**...** ) po prawej stronie nazwy źródła danych.
1. Z menu rozwijanego wybierz **Unsubscribe**.
1. Przejrzyj i wybierz **Kontynuuj** z poziomu okna dialogowego.

## <a name="update-the-client"></a>Aktualizacja klienta programu

Po udostępnieniu nowej wersji klienta zostanie wyświetlone powiadomienie przez klienta i Centrum akcji Windows. Wybierz powiadomienie, aby rozpocząć proces aktualizacji.
