---
title: Często zadawane pytania dotyczące wydajności SMB dla plików NetApp platformy Azure| Dokumenty firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące wydajności SMB dla plików NetApp platformy Azure.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460453"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Często zadawane pytania dotyczące wydajności SMB dla plików NetApp usługi Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące najlepszych rozwiązań dotyczących wydajności SMB dla plików NetApp usługi Azure.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Czy wielokanałowy SMB jest włączony w udziałach SMB? 

Tak, wielokanałowy SMB jest domyślnie włączony, zmiana wprowadzona na początku stycznia 2020. Wszystkie udziały SMB przed datą istniejących woluminów SMB miały włączoną funkcję, a wszystkie nowo utworzone woluminy będą również miały włączoną funkcję w momencie tworzenia. 

Aby skorzystać z funkcji wielokanałowych SMB, konieczne będzie zresetowanie dowolnego połączenia SMB ustanowionego przed włączeniem funkcji. Aby zresetować, można odłączyć i ponownie podłączyć udział SMB.

## <a name="is-rss-supported"></a>Czy obsługa RSS jest obsługiwana?

Tak, usługa Azure NetApp Files obsługuje skalowanie po stronie odbierania (RSS).

Po włączeniu wielokanałowego protokołu SMB klient SMB3 nawiązuje wiele połączeń TCP z serwerem SMB plików NetApp usługi Azure za pośrednictwem karty interfejsu sieciowego (NIC), która jest z obsługą pojedynczego RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Które wersje systemu Windows obsługują wielokanałowy SMB?

System Windows obsługuje wielokanałowy SMB od systemu Windows 2012, aby umożliwić najlepszą wydajność.  Zobacz [Wdrażanie wielokanałowych SMB](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) i [podstawy wielokanałowego SMB, aby](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) uzyskać szczegółowe informacje. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Czy moja maszyna wirtualna platformy Azure obsługuje RSS?

Aby sprawdzić, czy karty sieciowe maszyny wirtualnej `Get-SmbClientNetworkInterface` platformy Azure obsługują `RSS Capable`usługę RSS, uruchom to polecenie w następujący sposób i sprawdź pole: 

![Obsługa RSS dla maszyny wirtualnej platformy Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Czy pliki NetApp platformy Azure obsługują SMB Direct?

Nie, usługa Azure NetApp Files nie obsługuje funkcji SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Jakie są korzyści z wielokanałowego SMB? 

Funkcja wielokanałowa SMB umożliwia klientowi SMB3 ustanawianie puli połączeń za pośrednictwem jednej karty interfejsu sieciowego (NIC) lub wielu kart sieciowych oraz używanie ich do wysyłania żądań dla pojedynczej sesji SMB. Z kolei zgodnie z projektem SMB1 i SMB2 wymagają od klienta ustanowienia jednego połączenia i wysłania całego ruchu SMB dla danej sesji za pomocą tego połączenia. To pojedyncze połączenie ogranicza ogólną wydajność protokołu, którą można osiągnąć z jednego klienta.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Czy należy skonfigurować wiele kart sieciowych na moim kliencie dla SMB?

Nie. Klient SMB będzie odpowiadał liczbie kart sieciowych zwracanych przez serwer SMB.  Każdy wolumin magazynu jest dostępny z jednego i tylko jednego punktu końcowego magazynu.  Oznacza to, że tylko jedna karta sieciowa będzie używana dla danej relacji SMB.  

Jak wynika `Get-SmbClientNetworkInterace` z poniższych wyników, maszyna wirtualna ma dwa interfejsy sieciowe - 15 i 12.  Jak pokazano poniżej `Get-SmbMultichannelConnection`pod poleceniem , mimo że istnieją dwa karty SIECIOWE obsługujące RSS, w połączeniu z udziałem SMB używany jest tylko interfejs 12; interfejs 15 nie jest używany.

![Karta sieciowa obsługujący rss](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Czy tworzenie zespołu kart sieciowych jest obsługiwane na platformie Azure?

Tworzenie zespołu kart sieciowych nie jest obsługiwane na platformie Azure. Mimo że wiele interfejsów sieciowych są obsługiwane na maszynach wirtualnych platformy Azure, reprezentują one logiczne, a nie konstrukcji fizycznej. W związku z tym zapewniają one nie odporność na uszkodzenia.  Ponadto przepustowość dostępna dla maszyny wirtualnej platformy Azure jest obliczana dla samej maszyny, a nie dla dowolnego interfejsu sieciowego.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Jaka jest wydajność dla wielokanałowych SMB?

Następujące testy i wykresy pokazują moc wielokanałowych kart SMB w obciążeniach pojedynczych wystąpień.

### <a name="random-io"></a>Losowe we/wy  

Z SMB Multichannel wyłączone na kliencie, czyste 8-KiB odczytu i zapisu testy zostały wykonane przy użyciu FIO i 40-GiB zestaw roboczy.  Udział SMB został odłączony między każdym testem, z przyrostami liczby połączeń klienta `1``4`SMB na ustawienia interfejsu sieciowego RSS , ,`8`,`16`. `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` Testy pokazują, że domyślne `4` ustawienie jest wystarczające dla obciążeń intensywnie korzystających z we/wy; i `8` nie `16` miało żadnego wpływu. 

`netstat -na | findstr 445` Polecenie okazało się, że dodatkowe połączenia zostały `1` ustanowione z przyrostami od do `4` `8` i do `16`.  Cztery rdzenie procesora cpu zostały w pełni wykorzystane dla SMB `Per Processor Network Activity Cycles` podczas każdego testu, co zostało potwierdzone przez statystyki perfmon (nie uwzględnione w tym artykule).)

![Losowe testy we/wy](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Maszyna wirtualna platformy Azure nie ma wpływu na limity we/wy magazynu SMB (ani NFS).  Jak pokazano poniżej, typ wystąpienia D16 ma ograniczoną szybkość 32 000 dla buforowanych we/wy magazynu i 25 600 dla nieskłoniętych we/wy magazynu.  Jednak powyższy wykres pokazuje znacznie więcej we/wy w stosunku do SMB.

![Losowe porównanie we/wy](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekwencyjne we/wy 

Testy podobne do randomnych testów we/wy opisane powyżej zostały przeprowadzone z 64-KiB sekwencyjne we/wy. Chociaż wzrost liczby połączeń klienta na interfejs sieciowy RSS poza 4' nie miał zauważalnego wpływu na losowe we/wy, to samo nie dotyczy sekwencyjnego we/wy. Jak pokazano na poniższym wykresie, każdy wzrost jest skojarzony z odpowiednim wzrostem przepływności odczytu. Przepływność zapisu pozostała płaska ze względu na ograniczenia przepustowości sieci wprowadzone przez platformę Azure dla każdego typu wystąpienia/rozmiaru. 

![Sekwencyjne testy we/wy](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Platforma Azure wprowadza limity szybkości sieciowej dla każdego typu/rozmiaru maszyny wirtualnej. Limit stawek jest nakładany tylko na ruch wychodzący. Liczba kart sieciowych obecnych na maszynie wirtualnej nie ma wpływu na całkowitą przepustowość dostępną dla maszyny.  Na przykład typ wystąpienia D16 ma limit sieci nałożony 8000 Mbps (1000 MiB/s).  Jak pokazuje powyższy wykres sekwencyjny, limit wpływa na ruch wychodzący (zapisuje), ale nie odczyty wielokanałowe.

![Sekwencyjne porównanie we/wy](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Czy zaleca się przyspieszoną sieć?

Aby uzyskać maksymalną wydajność, zaleca się [skonfigurowanie przyspieszonej sieci tam,](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) gdzie to możliwe. Należy pamiętać o następujących kwestiach:  

* Portal Azure domyślnie włącza przyspieszoną sieć dla maszyn wirtualnych obsługujących tę funkcję.  Jednak inne metody wdrażania, takie jak Ansible i podobne narzędzia konfiguracyjne, mogą nie.  Brak włączenia przyspieszonej sieci może pochłonąć wydajność maszyny.  
* Jeśli przyspieszona sieć nie jest włączona w interfejsie sieciowym maszyny wirtualnej z powodu braku obsługi dla typu lub rozmiaru wystąpienia, pozostanie wyłączona przy większych typach wystąpień. W takich przypadkach potrzebna będzie ręczna interwencja.

## <a name="are-jumbo-frames-supported"></a>Czy ramki jumbo są obsługiwane?

Ramki jumbo nie są obsługiwane przez maszyny wirtualne platformy Azure.

## <a name="is-smb-signing-supported"></a>Czy podpisywanie SMB jest obsługiwane? 

Protokół SMB stanowi podstawę udostępniania plików i drukarek oraz innych operacji sieciowych, takich jak zdalna administracja systemem Windows. Aby zapobiegać atakom typu man-in-the-middle, które modyfikują pakiety SMB podczas przesyłania, protokół SMB obsługuje cyfrowe podpisywanie pakietów SMB. 

Podpisywanie SMB jest obsługiwane dla wszystkich wersji protokołu SMB, które są obsługiwane przez usługi Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Jaki jest wpływ podpisywania SMB?  

Podpisywanie SMB ma szkodliwy wpływ na wydajność SMB. Wśród innych potencjalnych przyczyn pogorszenia wydajności, cyfrowe podpisywanie każdego pakietu zużywa dodatkowy procesor po stronie klienta, jak pokazuje wyjście perfmon poniżej. W takim przypadku Core 0 wydaje się odpowiedzialny za SMB, w tym podpisywanie SMB.  Porównanie z niewiekanalnymi sekwencyjnymi numerami przepływności odczytu w poprzedniej sekcji pokazuje, że podpisywanie SMB zmniejsza ogólną przepływność z 875MiB/s do około 250MiB/s. 

![Wpływ na wydajność podpisywania SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Następne kroki  

- [Często zadawane pytania dotyczące plików NetApp platformy Azure](azure-netapp-files-faqs.md)
- Zobacz [usługi Azure NetApp Files: Managed Enterprise File Shares for SMB Workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) about using SMB file shares with Azure NetApp Files.See the Azure NetApp Files: Managed Enterprise File Shares for SMB Workloads about using SMB file shares with Azure NetApp Files.
