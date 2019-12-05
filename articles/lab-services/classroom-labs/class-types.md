---
title: Przykładowe typy klas w Azure Lab Services | Microsoft Docs
description: Zawiera typy klas, dla których można skonfigurować laboratoria przy użyciu Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: e1c5504b30c2784e8657ccc0dc4ec18689fe2a68
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806817"
---
# <a name="class-types-overview---azure-lab-services"></a>Przegląd typów klas — Azure Lab Services

Azure Lab Services umożliwia szybkie Konfigurowanie środowisk laboratoryjnych w chmurze. Artykuły w tej sekcji zawierają wskazówki dotyczące konfigurowania kilku typów laboratoriów zajęć przy użyciu Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Uczenie głębokie w przetwarzaniu języka naturalnego

Można skonfigurować laboratorium ukierunkowane na głębokie uczenie w ramach przetwarzania języka naturalnego (NLP) przy użyciu Azure Lab Services. Przetwarzanie języka naturalnego (NLP) jest formą sztucznej analizy (AI), która umożliwia komputerom z translacją, rozpoznawanie mowy i innymi funkcjami interpretacji języka. Studenci tworzący klasę NLPą mogą uzyskać maszynę wirtualną z systemem Linux, aby dowiedzieć się, jak zastosować algorytmy sieciowe neuronowych w celu opracowania modeli uczenia głębokiego, które są używane do analizowania zapisanych języków ludzkich.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium ukierunkowanego na głębokie uczenie w zakresie przetwarzania w języku naturalnym przy użyciu Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Skrypty powłoki w systemie Linux

Można skonfigurować laboratorium do uczenia skryptów powłoki w systemie Linux. Obsługa skryptów jest przydatną częścią administrowania systemem, która umożliwia administratorom uniknięcie powtarzających się zadań. W tym przykładowym scenariuszu Klasa obejmuje tradycyjne skrypty bash oraz ulepszone skrypty. Ulepszone skrypty to skrypty, które łączą bash polecenia i Ruby. Takie podejście umożliwia używanie języka Ruby do przekazywania danych wokół i bash poleceń w celu współdziałania z powłoką.

Studenci korzystający z tych klas skryptów otrzymują maszynę wirtualną z systemem Linux, aby poznać podstawy systemu Linux, a także zapoznać się z obsługą skryptów powłoki bash. Na maszynie wirtualnej z systemem Linux jest włączona funkcja dostępu do pulpitu zdalnego z zainstalowanym edytorem tekstu w języku [regedit](https://help.gnome.org/users/gedit/stable/) i [Visual Studio Code](https://code.visualstudio.com/) .

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etyczne działania hakerskie

Można skonfigurować laboratorium dla klasy, która koncentruje się na dowodowych etycznej działanie hakerskie. Testowanie penetracji, metoda stosowana przez społeczność etyczną działanie hakerskie, występuje, gdy ktoś próbuje uzyskać dostęp do systemu lub sieci w celu zademonstrowania luk w zabezpieczeniach.

W klasie etycznej działanie hakerskie studenci mogą uczyć się nowoczesnych technik obrony przed lukami w zabezpieczeniach. Każdy student otrzymuje maszynę wirtualną hosta systemu Windows Server, która ma dwie zagnieżdżone maszyny wirtualne — jedną maszynę wirtualną z obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) i inną maszynę z obrazem [Kali Linux](https://www.kali.org/) . Maszyna wirtualna Metasploitable jest używana do wykorzystania w celach.  Maszyna wirtualna z systemem Kali Linux zapewnia dostęp do narzędzi wymaganych do wykonywania zadań śledczej.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia klasy etycznej działanie hakerskie](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Skonfiguruj laboratorium ukierunkowane na głębokie uczenie się w przetwarzaniu języka naturalnego przy użyciu Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md)
- [Działanie hakerskie etyczny](class-type-ethical-hacking.md)
