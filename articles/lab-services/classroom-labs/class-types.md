---
title: Przykładowe typy klas w usługach Azure Lab Services | Dokumenty firmy Microsoft
description: Zawiera niektóre typy klas, dla których można skonfigurować laboratoria przy użyciu usługi Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 27619a69a1f7fbded8ce6430afc2b8e9a8b4a00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296731"
---
# <a name="class-types-overview---azure-lab-services"></a>Omówienie typów klas — usługi Azure Lab Services

Usługa Azure Lab Services umożliwia szybkie konfigurowanie środowisk laboratoryjnych w klasie w chmurze. Artykuły w tej sekcji zawierają wskazówki dotyczące konfigurowania kilku typów laboratoriów w klasie przy użyciu usług Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Głębokie uczenie się w przetwarzaniu języka naturalnego

Można skonfigurować laboratorium koncentrujące się na głębokim uczeniu w przetwarzaniu języka naturalnego (NLP) przy użyciu usługi Azure Lab Services. Przetwarzanie języka naturalnego (NLP) jest formą sztucznej inteligencji (AI), która umożliwia komputerom z możliwością tłumaczenia, rozpoznawania mowy i innych funkcji rozumienia języka. Uczniowie biorący klasę NLP otrzymują maszynę wirtualną systemu Linux (VM), aby dowiedzieć się, jak zastosować algorytmy sieci neuronowej do opracowywania modeli uczenia głębokiego, które są używane do analizowania napisanego języka ludzkiego.

Aby uzyskać szczegółowe informacje na temat konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium koncentrującego się na głębokim uczeniu w przetwarzaniu języka naturalnego przy użyciu usług Azure Lab Services.](class-type-deep-learning-natural-processing.md)

## <a name="shell-scripting-on-linux"></a>Skrypty powłoki w systemie Linux

Można skonfigurować laboratorium do nauczania skryptów powłoki na Linuksie. Skrypty są użyteczną częścią administracji systemowej, która pozwala administratorom uniknąć powtarzających się zadań. W tym przykładowym scenariuszu klasa obejmuje tradycyjnych skryptów bash i skryptów rozszerzonych. Ulepszone skrypty to skrypty łączące polecenia bash i Ruby. Takie podejście pozwala Ruby przekazać dane wokół i bash polecenia do interakcji z powłoki.

Uczniowie biorący te klasy skryptów otrzymują maszynę wirtualną Linuksa, aby nauczyć się podstaw linuksa, a także zapoznać się ze skryptami powłoki bash. Maszyna wirtualna Systemu Linux jest wyposażona w dostęp do pulpitu zdalnego z zainstalowanym [edytorami](https://help.gnome.org/users/gedit/stable/) tekstu gedit i [Visual Studio Code.](https://code.visualstudio.com/)

Aby uzyskać szczegółowe informacje na temat konfigurowania tego typu laboratorium, zobacz [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etyczne działania hakerskie

Możesz założyć laboratorium dla klasy, która koncentruje się na kryminalistyce etycznego hakowania. Testy penetracyjne, praktyka stosowana przez etyczną społeczność hakerów, ma miejsce, gdy ktoś próbuje uzyskać dostęp do systemu lub sieci, aby wykazać luki, które może wykorzystać złośliwy atakujący.

W klasie etycznego hakowania uczniowie mogą nauczyć się nowoczesnych technik obrony przed lukami w zabezpieczeniach. Każdy student otrzymuje maszynę wirtualną hosta systemu Windows Server, która ma dwie zagnieżdżone maszyny wirtualne — jedną maszynę wirtualną z obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) i inną maszynę z obrazem [Kali Linux.](https://www.kali.org/) Metasploitable maszyny wirtualnej jest używany do wykorzystania celów.  Maszyna wirtualna Kali Linux zapewnia dostęp do narzędzi potrzebnych do wykonywania zadań kryminalistycznych.

Aby uzyskać szczegółowe informacje na temat konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do nauczania etycznego hackingu.](class-type-ethical-hacking.md)

## <a name="database-management"></a>Zarządzanie bazami danych
Koncepcje baz danych są jednym z kursów wprowadzających prowadzonych w większości wydziałów informatyki w college'u. W usłudze Azure Lab Services można skonfigurować laboratorium dla klasy zarządzania podstawowymi bazami danych. Na przykład można skonfigurować szablon maszyny wirtualnej w laboratorium za pomocą serwera bazy danych [MySQL](https://www.mysql.com/) lub serwera [SQL Server 2019.](https://www.microsoft.com/sql-server/sql-server-2019)

Aby uzyskać szczegółowe informacje na temat konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do nauczania zarządzania bazami danych dla relacyjnych baz danych](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Notesy Pythona i Jupytera
Maszynę szablonów można skonfigurować w usłudze Azure Lab Services za pomocą narzędzi potrzebnych do nauczenia uczniów, jak używać [notesów Jupyter.](http://jupyter-notebook.readthedocs.io) Jupyter Notebooks to projekt typu open source, który pozwala łatwo łączyć tekst sformatowany i wykonywalny kod źródłowy [Pythona](https://www.python.org/) na jednym płótnie zwanym notesem. Uruchomienie notesu powoduje liniowy rekord wejść i wyjść.  Te dane wyjściowe mogą zawierać tekst, tabele informacji, wykresy punktowe i inne.

Aby uzyskać szczegółowe informacje na temat konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do nauczania nauki o danych za pomocą notesów Pythona i Jupytera](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Tworzenie aplikacji mobilnych za pomocą programu Android Studio
Można skonfigurować laboratorium w usłudze Azure Lab Services, aby uczyć wprowadzającej klasy tworzenia aplikacji mobilnych. Ta klasa koncentruje się na aplikacjach mobilnych na Androida, które można opublikować w [Sklepie Google Play.](https://play.google.com/store/apps)  Uczniowie uczą się, jak używać [Android Studio](https://developer.android.com/studio) do tworzenia aplikacji.  [Visual Studio Emulator dla systemu Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) jest używany do testowania aplikacji lokalnie.

Aby uzyskać szczegółowe informacje na temat konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do nauczania tworzenia aplikacji mobilnych w systemie Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Skonfiguruj laboratorium koncentrujące się na głębokim uczeniu w przetwarzaniu języka naturalnego przy użyciu usług Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md)
- [Etyczne działania hakerskie](class-type-ethical-hacking.md)