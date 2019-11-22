---
title: Omówienie usługi Azure Spatial Anchors
description: Dowiedz się, jak usługa Azure Spatial Anchors ułatwia opracowanie międzyplatformowych środowisk rzeczywistości mieszanej.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 503004cba0d4109adcfee62f7acd108cbcb73eb6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277157"
---
# <a name="azure-spatial-anchors-overview"></a>Omówienie usługi Azure Spatial Anchors

Witamy w usłudze Azure Spatial Anchors. Usługa Azure Spatial Anchors udostępnia deweloperom podstawowe funkcje umożliwiające tworzenie aplikacji rzeczywistości mieszanej z obsługą orientacji przestrzennej. Te aplikacje mogą obsługiwać urządzenia Microsoft HoloLens, urządzenia z systemem iOS wspierające platformę ARKit i urządzenia z systemem Android wspierające platformę ARCore. Usługa Azure Spatial Anchors umożliwia deweloperom pracę z platformami rzeczywistości mieszanej w celu obserwowania przestrzeni, wyznaczania dokładnych punktów orientacyjnych i przywoływania tych punktów orientacyjnych z obsługiwanych urządzeń.
Te dokładne punkty orientacyjne są określane jako kotwice przestrzenne.

![Obsługa wielu platform](./media/cross-platform.png)

## <a name="examples"></a>Przykłady

Przykładowe przypadki użycia możliwe dzięki zastosowaniu usługi Spatial Anchors obejmują:

- [Środowiska z wieloma użytkownikami](tutorials/tutorial-share-anchors-across-devices.md). Usługa Spatial Anchors ułatwia osobom w tym samym miejscu wzięcie udziału w aplikacjach rzeczywistości mieszanej dla wielu użytkowników. Na przykład dwie osoby mogą uruchomić grę w szachy w rzeczywistości mieszanej, umieszczając wirtualną szachownicę na stole. Następnie, nakierowując swoje urządzenia na stół, mogą wspólnie wyświetlać i wchodzić w interakcje z wirtualną szachownicą.

- [Znajdowanie drogi](concepts/anchor-relationships-way-finding.md). Deweloperzy mogą także łączyć kotwice przestrzenne, tworząc między nimi relacje. Na przykład aplikacja może obejmować środowisko z co najmniej dwoma punktami orientacyjnymi, z którymi użytkownik musi wejść w interakcję, aby ukończyć zadanie. Te punkty orientacyjne mogą być tworzone jako połączone. Później, gdy użytkownik wykonuje zadanie wieloetapowe, aplikacja może zapytać o kotwice znajdujące się w pobliżu bieżącej kotwicy, aby skierować użytkownika do następnego kroku w zadaniu.

- [Utrwalanie wirtualnej zawartości w świecie rzeczywistym](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Aplikacja może umożliwić użytkownikowi umieszczenie wirtualnego kalendarza na ścianie sali konferencyjnej, który użytkownicy mogą zobaczyć za pomocą aplikacji na telefon lub urządzenia HoloLens. W środowisku przemysłowym użytkownik może otrzymywać informacje kontekstowe o maszynie, kierując na nią aparat obsługiwanego urządzenia.

Usługa Azure Spatial Anchors składa się z usługi zarządzanej i zestawów SDK klienta dla obsługiwanych platform urządzeń. Poniższe sekcje zawierają informacje o rozpoczynaniu tworzenia aplikacji przy użyciu usługi Azure Spatial Anchors.

## <a name="next-steps"></a>Następne kroki

Tworzenie pierwszej aplikacji z użyciem usługi Spatial Anchors.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
