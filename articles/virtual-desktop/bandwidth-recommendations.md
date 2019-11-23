---
title: Zalecenia dotyczące przepustowości dla sesji zdalnych — Azure
description: Dostępne zalecenia dotyczące przepustowości dla sesji zdalnych.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: helohr
ms.openlocfilehash: 8352858b9fd6059257b5ba7637822f85c56eb070
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802625"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Zalecenia dotyczące przepustowości dla sesji zdalnych

W przypadku korzystania z zdalnej sesji systemu Windows dostępną przepustowość sieci znacznie wpływa na jakość środowiska. Różne aplikacje i rozdzielczości ekranu wymagają różnych konfiguracji sieci, dlatego należy się upewnić, że sieć jest skonfigurowana do Twoich potrzeb.

>[!NOTE]
>Poniższe zalecenia dotyczą sieci z mniejszą niż 0,1% strat.

## <a name="applications"></a>Aplikacje

Poniższa tabela zawiera listę minimalnych zalecanych przepustowości dla sprawnego środowiska użytkownika. 

|Obciążenie        |Przykładowe aplikacje                                                                                           |Zalecana przepustowość|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Wykonywanie pojedynczych zadań     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1,5&nbsp;MB/s        |
|Proces roboczy pakietu Office   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, podgląd zdjęć                                        |3&nbsp;MB/s          |
|Pracownik merytoryczny|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer, Java                                  |5&nbsp;MB/s          |
|Pracownik zaawansowany    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer, Java, CAD/kamera, ilustracja/publikowanie|15&nbsp;MB/s         |

>[!NOTE]
>Te zalecenia są stosowane niezależnie od liczby użytkowników, którzy znajdują się w sesji.

Należy pamiętać, że obciążenie w sieci zależy od szybkości klatek danych wyjściowych i rozdzielczości ekranu. Jeśli zwiększy się szybkość klatek lub rozdzielczość ekranu, to również wymaganie dotyczące przepustowości. Na przykład lekkie obciążenie z wyświetlaczem o wysokiej rozdzielczości wymaga większej dostępnej przepustowości niż lekkie obciążenie z normalnymi lub niskimi rozwiązaniami.

Inne scenariusze mogą ulec zmianie w zależności od sposobu ich użycia, na przykład:

- Konferencje głosowe lub wideo
- Komunikacja w czasie rzeczywistym
- Wideo na temat strumienia 4K

Upewnij się, że testujesz te scenariusze we wdrożeniu przy użyciu narzędzi do symulacji, takich jak login VSI. Zapoznaj się z różnicą rozmiaru obciążenia, Uruchom testy obciążeniowe i przetestuj typowe scenariusze użytkowników w sesjach zdalnych, aby lepiej zrozumieć wymagania sieci. 

## <a name="display-resolutions"></a>Rozdzielczości ekranu

Różne rozdzielczości wyświetlania wymagają różnych dostępnych przepustowości. W poniższej tabeli wymieniono przepustowość, którą zalecamy dla sprawnego środowiska użytkownika przy typowych rozdzielczościach wyświetlania z szybkością klatek 30 klatek na sekundę (FPS). Te zalecenia dotyczą scenariuszy z pojedynczym i wieloma użytkownikami. Należy pamiętać, że scenariusze obejmujące szybkość klatek na 30 fps, takie jak odczytywanie tekstu statycznego, wymagają mniej dostępnej przepustowości. 

|Typowe rozdzielczości wyświetlania na 30 fps    |Zalecana przepustowość|
|-----------------------------------------|---------------------|
|Około 1024 × 768 pikseli                      |1,5 MB/s             |
|Około 1280 × 720 pikseli                      |3 MB/s               |
|Około 1920 × 1080 pikseli                     |5 MB/s               |
|Około 3840 × 2160 pikseli (4K)                |15 MB/s              |

>[!NOTE]
>Te zalecenia są stosowane niezależnie od liczby użytkowników, którzy znajdują się w sesji.

## <a name="additional-resources"></a>Dodatkowe zasoby

Region świadczenia usługi Azure, w którym jesteś w stanie, może mieć wpływ na środowisko użytkownika tak samo jak w przypadku warunków sieci. Zapoznaj się z tematem [szacowania pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) , aby dowiedzieć się więcej.
