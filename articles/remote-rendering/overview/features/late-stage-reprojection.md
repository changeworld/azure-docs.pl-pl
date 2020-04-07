---
title: Ponowna proces opóźniania się w późnym etapie
description: Informacje na temat ponownego wystawiania na późnym etapie i sposobu korzystania z niej.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680988"
---
# <a name="late-stage-reprojection"></a>Ponowna proces opóźniania się w późnym etapie

*Ponowne wystawianie na późnym etapie* (LSR) to funkcja sprzętowa, która pomaga ustabilizować hologramy podczas przenoszenia się użytkownika.

Oczekuje się, że modele statyczne będą wizualnie utrzymywać swoją pozycję podczas poruszania się po nich. Jeśli wydają się być niestabilne, to zachowanie może wskazywać na problemy LSR. Należy pamiętać, że dodatkowe dynamiczne przekształcenia, takie jak animacje lub widoki rozłowienia, mogą maskować to zachowanie.

Możesz wybrać jeden z dwóch różnych trybów LSR, a mianowicie **Planar LSR** lub **Depth LSR**. Który z nich jest aktywny zależy od tego, czy aplikacja kliencka przesyła bufor głębokości.

Oba tryby LSR poprawiają stabilność hologramu, chociaż mają swoje odrębne ograniczenia. Zacznij od wypróbowania Głębokość LSR, ponieważ jest to prawdopodobnie dając lepsze wyniki w większości przypadków.

## <a name="choose-lsr-mode-in-unity"></a>Wybieranie trybu LSR w unity

W edytorze Unity przejdź do *pozycji Ustawienia kompilacji > plików*. Wybierz *pozycję Ustawienia odtwarzacza* w lewym dolnym dolnym roku, a następnie zaznacz w obszarze *Ustawienia > XR player > zestawy SDK rzeczywistości wirtualnej > rzeczywistości mieszanej systemu Windows,* czy zaznaczono opcję **Włącz udostępnianie buforu głębokości:**

![Flaga Włączona funkcja udostępniania buforu głębokości](./media/unity-depth-buffer-sharing-enabled.png)

Jeśli tak jest, aplikacja będzie używać depth LSR, w przeciwnym razie użyje Planar LSR.

## <a name="depth-lsr"></a>Głębokość LSR

Aby głębokość LSR do pracy, aplikacja kliencka musi dostarczyć prawidłowy bufor głębokości, który zawiera wszystkie odpowiednie geometrii do rozważenia podczas LSR.

Głębokość LSR próbuje ustabilizować klatkę wideo na podstawie zawartości dostarczonego buforu głębokości. W związku z tym zawartość, która nie została wyrenderowana do niego, takich jak obiekty przezroczyste, nie mogą być dostosowane przez LSR i może wykazywać niestabilność i artefakty ponownego przetwarzania.

## <a name="planar-lsr"></a>Planar LSR

Planar LSR nie ma informacji o głębokości na piksel, jak głębokość LSR nie. Zamiast tego ponownie przedstawia całą zawartość na podstawie płaszczyzny, która musi dostarczyć każdą ramkę.

Planar LSR projektuje te obiekty najlepiej, które znajdują się w pobliżu dostarczonej płaszczyzny. Im dalej znajduje się obiekt, tym bardziej niestabilny będzie wyglądał. Podczas gdy głębokość LSR jest lepsza w ponownym współtworzeniu obiektów na różnych głębokościach, Planar LSR może działać lepiej dla zawartości dobrze wyrównywania z płaszczyzną.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurowanie planarnego LSR w unity

Parametry płaszczyzny pochodzą z tak zwanego *punktu ostrości,* przez `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`który należy zapewnić każdą klatkę. Zobacz [interfejs API unity focus point, aby](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) uzyskać szczegółowe informacje. Jeśli nie ustawisz punktu ostrości, zostanie wybrany rezerwowy. Jednak automatyczny rezerwowy często prowadzi do nieoptymalnych wyników.

Punkt ostrości można obliczyć samodzielnie, choć może to mieć sens, aby oprzeć go na tym obliczonym przez hosta renderowania zdalnego. Zadzwoń, `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` aby to uzyskać. Użytkownik jest proszony o podanie ramki współrzędnych, w której można wyrazić punkt ostrości. W większości przypadków po prostu chcesz podać `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` wynik stąd.

Zazwyczaj zarówno klienta, jak i hosta renderowania zawartości, że druga strona nie jest świadomy, takich jak elementy interfejsu użytkownika na kliencie. W związku z tym może mieć sens, aby połączyć zdalnego punktu fokusu z lokalnie obliczony.

Punkty ostrości obliczone w dwóch kolejnych klatkach mogą być zupełnie inne. Po prostu za ich pomocą jako-jest może prowadzić do hologramów wydaje się być skakanie. Aby zapobiec takiemu zachowaniu, zaleca się interpolację między poprzednim i bieżącym punktem ostrości.

## <a name="next-steps"></a>Następne kroki

* [Kwerendy wydajności po stronie serwera](performance-queries.md)
