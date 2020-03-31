---
title: Wprowadzanie certyfikatów X.509 w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: Jak przetaczają certyfikaty X.509 za pomocą wystąpienia usługi obsługi urządzeń (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974891"
---
# <a name="how-to-roll-x509-device-certificates"></a>Jak rolka certyfikatów urządzeń X.509

Podczas cyklu życia rozwiązania IoT należy rolki certyfikatów. Dwa główne powody stopniowego sprawdzania certyfikatów to naruszenie zabezpieczeń i wygaśnięcie certyfikatów. 

Certyfikaty stopniowe to najlepsze rozwiązanie w zakresie zabezpieczeń, które pomagają zabezpieczyć system w przypadku naruszenia zabezpieczeń. W ramach [metodologii zakładania naruszenia](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)firma Microsoft opowiada się za koniecznością wprowadzenia reaktywnych procesów zabezpieczeń wraz ze środkami zapobiegawczymi. Stopniowanie certyfikatów urządzeń powinno być uwzględnione w ramach tych procesów zabezpieczeń. Częstotliwość, w której certyfikaty są przerzucone, będzie zależeć od potrzeb w zakresie zabezpieczeń rozwiązania. Klienci korzystający z rozwiązań obejmujących bardzo wrażliwe dane mogą codziennie przeprowadzać certyfikaty, podczas gdy inni co kilka lat rolują swoje certyfikaty.

Certyfikaty urządzeń stopniowych będą obejmować aktualizowanie certyfikatu przechowywanego na urządzeniu i centrum IoT Hub. Następnie urządzenie może ponownie aprowizować się za pomocą centrum IoT przy użyciu [normalnej automatycznej inicjowania obsługi administracyjnej](concepts-auto-provisioning.md) za pomocą usługi inicjowania obsługi administracyjnej urządzeń.


## <a name="obtain-new-certificates"></a>Uzyskiwanie nowych certyfikatów

Istnieje wiele sposobów uzyskiwania nowych certyfikatów dla urządzeń IoT. Obejmują one uzyskiwanie certyfikatów z fabryki urządzeń, generowanie własnych certyfikatów i zarządzanie tworzeniem certyfikatów przez firmę trzecią. 

Certyfikaty są podpisywane przez siebie nawzajem w celu utworzenia łańcucha zaufania od certyfikatu głównego urzędu certyfikacji do [certyfikatu typu liść](concepts-security.md#end-entity-leaf-certificate). Certyfikat podpisywania to certyfikat używany do podpisywania certyfikatu liścia na końcu łańcucha zaufania. Certyfikat podpisywania może być certyfikatem głównego urzędu certyfikacji lub certyfikatem pośrednim w łańcuchu zaufania. Aby uzyskać więcej informacji, zobacz [X.509 certyfikaty](concepts-security.md#x509-certificates).
 
Istnieją dwa różne sposoby uzyskania certyfikatu podpisywania. Pierwszym sposobem, który jest zalecany dla systemów produkcyjnych, jest zakup certyfikatu podpisywania od głównego urzędu certyfikacji (CA). W ten sposób łańcuchy zabezpieczeń w dół do zaufanego źródła. 

Drugim sposobem jest utworzenie własnych certyfikatów X.509 za pomocą narzędzia takiego jak OpenSSL. Takie podejście jest idealne do testowania certyfikatów X.509, ale zapewnia kilka gwarancji dotyczących zabezpieczeń. Firma Microsoft zaleca używanie tej metody tylko do testowania, chyba że użytkownik jest przygotowany do działania jako własny dostawca urzędu certyfikacji.
 

## <a name="roll-the-certificate-on-the-device"></a>Rolka certyfikatu na urządzeniu

Certyfikaty na urządzeniu powinny być zawsze przechowywane w bezpiecznym miejscu, takim jak [sprzętowy moduł zabezpieczeń (HSM).](concepts-device.md#hardware-security-module) Sposób, w jaki rolka certyfikaty urządzeń będzie zależeć od tego, jak zostały one utworzone i zainstalowane w urządzeniach w pierwszej kolejności. 

Jeśli certyfikaty zostały odebrane przez stronę trzecią, należy sprawdzić, w jaki sposób zwijają swoje certyfikaty. Proces ten może być zawarty w porozumieniu z nimi lub może być odrębną usługą, którą oferują. 

Jeśli zarządzasz własnymi certyfikatami urządzeń, musisz utworzyć własny potok do aktualizowania certyfikatów. Upewnij się, że zarówno stare, jak i nowe certyfikaty liści mają taką samą nazwę pospolitą (CN). Dzięki tej samej cn urządzenie może ponownie aprowizji się bez tworzenia zduplikowany rekord rejestracji. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Rolka certyfikatu w centrum IoT

Certyfikat urządzenia można ręcznie dodać do centrum IoT hub. Certyfikat można również zautomatyzować przy użyciu wystąpienia usługi inicjowania obsługi administracyjnej urządzeń. W tym artykule zakładamy, że wystąpienie usługi inicjowania obsługi administracyjnej urządzeń jest używane do obsługi automatycznego inicjowania obsługi administracyjnej.

Gdy urządzenie jest początkowo aprowizacji za pośrednictwem automatycznej inicjowania obsługi administracyjnej, uruchamia się i kontaktuje się z usługą inicjowania obsługi administracyjnej. Usługa inicjowania obsługi administracyjnej odpowiada, wykonując sprawdzanie tożsamości przed utworzeniem tożsamości urządzenia w centrum IoT hub przy użyciu certyfikatu liścia urządzenia jako poświadczenia. Usługa inicjowania obsługi administracyjnej następnie informuje urządzenie, do którego centrum IoT jest przypisane, a urządzenie używa swojego certyfikatu liścia do uwierzytelniania i łączenia się z centrum IoT hub. 

Po wprowadzeniu nowego certyfikatu liścia do urządzenia nie może już łączyć się z centrum IoT hub, ponieważ używa nowego certyfikatu do nawiązania połączenia. Centrum IoT rozpoznaje tylko urządzenie ze starym certyfikatem. Wynikiem próby połączenia urządzenia będzie "nieautoryzowany" błąd połączenia. Aby rozwiązać ten błąd, należy zaktualizować wpis rejestracji dla urządzenia, aby uwzględnić nowy certyfikat typu liść urządzenia. Następnie usługa inicjowania obsługi administracyjnej może zaktualizować informacje rejestru urządzenia Usługi IoT Hub zgodnie z potrzebami, gdy urządzenie jest ponownie aprowizacji. 

Jednym z możliwych wyjątków od tego błędu połączenia będzie scenariusz, w którym utworzono [grupę rejestracji](concepts-service.md#enrollment-group) dla urządzenia w usłudze inicjowania obsługi administracyjnej. W takim przypadku, jeśli nie są toczenia certyfikatów głównych lub pośrednich w łańcuchu zaufania certyfikatów urządzenia, urządzenie zostanie rozpoznane, jeśli nowy certyfikat jest częścią łańcucha zaufania zdefiniowanego w grupie rejestracji. Jeśli ten scenariusz powstaje w reakcji na naruszenie zabezpieczeń, należy co najmniej czarnej listy certyfikatów określonych urządzeń w grupie, które są uważane za naruszone. Aby uzyskać więcej informacji, zobacz [Czarna lista określonych urządzeń w grupie rejestracji](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Aktualizowanie wpisów rejestracji dla certyfikatów walcowanych odbywa się na stronie **Zarządzanie rejestracjami.** Aby uzyskać dostęp do tej strony, wykonaj następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń usługi usługi usługi IoT Hub, które ma wpis rejestracji dla urządzenia.

2. Kliknij pozycję **Zarządzanie rejestracjami**.

    ![Zarządzanie rejestracjami](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Sposób obsługi aktualizowania wpisu rejestracji zależy od tego, czy używasz rejestracji indywidualnych, czy rejestracji grupowych. Również zalecane procedury różnią się w zależności od tego, czy certyfikaty są stopniowane z powodu naruszenia zabezpieczeń lub wygaśnięcia certyfikatu. W poniższych sekcjach opisano sposób obsługi tych aktualizacji.


## <a name="individual-enrollments-and-security-breaches"></a>Indywidualne rejestracje i naruszenia zabezpieczeń

Jeśli certyfikaty są stopniowe w odpowiedzi na naruszenie zabezpieczeń, należy użyć następujące podejście, które usuwa bieżący certyfikat natychmiast:

1. Kliknij **pozycję Rejestracje indywidualne**i kliknij pozycję Identyfikator rejestracji na liście. 

2. Kliknij przycisk **Usuń bieżący certyfikat,** a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat, który ma zostać przekazany dla wpisu rejestracji. Kliknij **przycisk Zapisz** po zakończeniu.

    Te kroki należy wykonać dla certyfikatu podstawowego i pomocniczego, jeśli oba są zagrożone.

    ![Zarządzanie indywidualnymi rejestracjami](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Po usunięciu certyfikatu, którego bezpieczeństwo zostało usunięte z usługi inicjowania obsługi administracyjnej, certyfikat może nadal służyć do nawiązywania połączeń urządzeń z centrum IoT hub, o ile istnieje tam rejestracja urządzenia. Możesz rozwiązać ten problem na dwa sposoby: 

    Pierwszym sposobem byłoby ręczne przejście do centrum IoT hub i natychmiastowe usunięcie rejestracji urządzenia skojarzonej z certyfikatem, który został naruszony. Następnie, gdy urządzenie ponownie zaszei się z zaktualizowanym certyfikatem, zostanie utworzona nowa rejestracja urządzenia.     

    ![Usuwanie rejestracji urządzeń centrum IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Drugim sposobem byłoby użycie obsługi ponownego obsługi do ponownego obsługi urządzenia do tego samego centrum IoT hub. Takie podejście może służyć do zastąpienia certyfikatu rejestracji urządzenia w centrum IoT hub. Aby uzyskać więcej informacji, zobacz [Jak ponownie aprowizji urządzeń](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Rejestracje indywidualne i wygaśnięcie certyfikatu

Jeśli certyfikaty są stopniowe do obsługi wygaśnięcia certyfikatów, należy użyć konfiguracji certyfikatu pomocniczego w następujący sposób, aby skrócić czas przestojów dla urządzeń próbujących aprowizować.

Później, gdy certyfikat pomocniczy również zbliża się do wygaśnięcia i musi zostać zwinięty, można obrócić do przy użyciu konfiguracji podstawowej. Obracanie między certyfikatami podstawowymi i pomocniczymi w ten sposób skraca przestoje urządzeń próbujących aprowizować.


1. Kliknij **pozycję Rejestracje indywidualne**i kliknij pozycję Identyfikator rejestracji na liście. 

2. Kliknij **pozycję Certyfikat pomocniczy,** a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat, który ma zostać przekazany dla wpisu rejestracji. Kliknij przycisk **Zapisz**.

    ![Zarządzanie poszczególnymi rejestracjami przy użyciu certyfikatu pomocniczego](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Później, gdy certyfikat podstawowy wygasł, wróć i usuń ten certyfikat podstawowy, klikając przycisk **Usuń bieżący certyfikat.**

## <a name="enrollment-groups-and-security-breaches"></a>Grupy rejestracji i naruszenia zabezpieczeń

Aby zaktualizować rejestrację grupy w odpowiedzi na naruszenie zabezpieczeń, należy użyć jednego z następujących metod, które natychmiast usuną bieżący główny urząd certyfikacji lub certyfikat pośredni.

#### <a name="update-compromised-root-ca-certificates"></a>Aktualizowanie certyfikatów głównego urzędu certyfikacji, których bezpieczeństwo zostało naruszone

1. Kliknij kartę **Certyfikaty** dla wystąpienia usługi inicjowania obsługi administracyjnej urządzeń.

2. Kliknij naruszony certyfikat na liście, a następnie kliknij przycisk **Usuń.** Potwierdź usunięcie, wprowadzając nazwę certyfikatu i kliknij przycisk **OK**. Powtórz ten proces dla wszystkich certyfikatów, których bezpieczeństwo zostało naruszone.

    ![Usuwanie głównego certyfikatu urzędu certyfikacji](./media/how-to-roll-certificates/delete-root-cert.png)

3. Wykonaj kroki opisane w [temacie Konfigurowanie zweryfikowanych certyfikatów urzędu certyfikacji](how-to-verify-certificates.md) w celu dodawania i weryfikowania nowych certyfikatów głównych urzędów certyfikacji.

4. Kliknij kartę **Zarządzanie rejestracjami** dla wystąpienia usługi inicjowania obsługi administracyjnej urządzeń, a następnie kliknij listę **Grupy rejestracji.** Kliknij nazwę grupy rejestracji na liście.

5. Kliknij pozycję **Certyfikat urzędu certyfikacji**i wybierz nowy główny certyfikat urzędu certyfikacji. Następnie kliknij przycisk **Zapisz**. 

    ![Wybieranie nowego głównego certyfikatu urzędu certyfikacji](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Po usunięciu certyfikatu, którego bezpieczeństwo zostało usunięte z usługi inicjowania obsługi administracyjnej, certyfikat może nadal służyć do nawiązywania połączeń urządzeń z centrum IoT hub, o ile istnieje tam rejestracja urządzeń. Możesz rozwiązać ten problem na dwa sposoby: 

    Pierwszym sposobem byłoby ręczne przejście do centrum IoT hub i natychmiastowe usunięcie rejestracji urządzenia skojarzonej z certyfikatem, który został naruszony. Następnie, gdy urządzenia ponownie udostępnić zaktualizowane certyfikaty, zostanie utworzona nowa rejestracja urządzenia dla każdego z nich.     

    ![Usuwanie rejestracji urządzeń centrum IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Drugim sposobem byłoby użycie obsługi ponownego obsługi administracyjnej w celu ponownego obsługi urządzeń do tego samego centrum IoT hub. Takie podejście może służyć do zastępowania certyfikatów dla rejestracji urządzeń w centrum IoT hub. Aby uzyskać więcej informacji, zobacz [Jak ponownie aprowizji urządzeń](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Aktualizacja certyfikatów pośrednich, których bezpieczeństwo zostało naruszone

1. Kliknij **pozycję Grupy rejestracji**, a następnie kliknij nazwę grupy na liście. 

2. Kliknij pozycję **Certyfikat pośredni**i **usuń bieżący certyfikat**. Kliknij ikonę folderu, aby przejść do nowego certyfikatu pośredniego, który ma zostać przekazany dla grupy rejestracji. Po zakończeniu **kliknij pozycję Zapisz.** Te kroki powinny zostać wykonane dla certyfikatu podstawowego i pomocniczego, jeśli oba są zagrożone.

    Ten nowy certyfikat pośredni powinien być podpisany przez zweryfikowany certyfikat głównego urzędu certyfikacji, który został już dodany do usługi inicjowania obsługi administracyjnej. Aby uzyskać więcej informacji, zobacz [X.509 certyfikaty](concepts-security.md#x509-certificates).

    ![Zarządzanie indywidualnymi rejestracjami](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Po usunięciu certyfikatu, którego bezpieczeństwo zostało usunięte z usługi inicjowania obsługi administracyjnej, certyfikat może nadal służyć do nawiązywania połączeń urządzeń z centrum IoT hub, o ile istnieje tam rejestracja urządzeń. Możesz rozwiązać ten problem na dwa sposoby: 

    Pierwszym sposobem byłoby ręczne przejście do centrum IoT hub i natychmiastowe usunięcie rejestracji urządzenia skojarzonej z certyfikatem, który został naruszony. Następnie, gdy urządzenia ponownie udostępnić zaktualizowane certyfikaty, zostanie utworzona nowa rejestracja urządzenia dla każdego z nich.     

    ![Usuwanie rejestracji urządzeń centrum IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Drugim sposobem byłoby użycie obsługi ponownego obsługi administracyjnej w celu ponownego obsługi urządzeń do tego samego centrum IoT hub. Takie podejście może służyć do zastępowania certyfikatów dla rejestracji urządzeń w centrum IoT hub. Aby uzyskać więcej informacji, zobacz [Jak ponownie aprowizji urządzeń](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupy rejestracji i wygaśnięcie certyfikatu

W przypadku stopniowego sprawdzania certyfikatów w celu obsługi wygaśnięcia certyfikatów należy użyć konfiguracji certyfikatu pomocniczego w następujący sposób, aby zapewnić brak przestojów dla urządzeń próbujących aprowizować.

Później, gdy certyfikat pomocniczy również zbliża się do wygaśnięcia i musi zostać zwinięty, można obrócić do przy użyciu konfiguracji podstawowej. Obracanie między certyfikatami podstawowymi i pomocniczymi w ten sposób zapewnia brak przestojów dla urządzeń próbujących aprowizować. 

#### <a name="update-expiring-root-ca-certificates"></a>Aktualizowanie wygasających certyfikatów głównego urzędu certyfikacji

1. Wykonaj kroki opisane w [temacie Konfigurowanie zweryfikowanych certyfikatów urzędu certyfikacji](how-to-verify-certificates.md) w celu dodawania i weryfikowania nowych certyfikatów głównych urzędów certyfikacji.

2. Kliknij kartę **Zarządzanie rejestracjami** dla wystąpienia usługi inicjowania obsługi administracyjnej urządzeń, a następnie kliknij listę **Grupy rejestracji.** Kliknij nazwę grupy rejestracji na liście.

3. Kliknij pozycję **Certyfikat urzędu certyfikacji**i wybierz nowy certyfikat głównego urzędu certyfikacji w konfiguracji **certyfikatu pomocniczego.** Następnie kliknij przycisk **Zapisz**. 

    ![Wybieranie nowego głównego certyfikatu urzędu certyfikacji](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Później po wygaśnięciu certyfikatu podstawowego kliknij kartę **Certyfikaty** dla wystąpienia usługi inicjowania obsługi administracyjnej urządzeń. Kliknij wygasły certyfikat na liście, a następnie kliknij przycisk **Usuń.** Potwierdź usunięcie, wprowadzając nazwę certyfikatu i kliknij przycisk **OK**.

    ![Usuwanie głównego certyfikatu urzędu certyfikacji](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aktualizacja wygasających certyfikatów pośrednich


1. Kliknij **pozycję Grupy rejestracji**i kliknij nazwę grupy na liście. 

2. Kliknij **pozycję Certyfikat pomocniczy,** a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat, który ma zostać przekazany dla wpisu rejestracji. Kliknij przycisk **Zapisz**.

    Ten nowy certyfikat pośredni powinien być podpisany przez zweryfikowany certyfikat głównego urzędu certyfikacji, który został już dodany do usługi inicjowania obsługi administracyjnej. Aby uzyskać więcej informacji, zobacz [X.509 certyfikaty](concepts-security.md#x509-certificates).

   ![Zarządzanie poszczególnymi rejestracjami przy użyciu certyfikatu pomocniczego](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Później, gdy certyfikat podstawowy wygasł, wróć i usuń ten certyfikat podstawowy, klikając przycisk **Usuń bieżący certyfikat.**


## <a name="reprovision-the-device"></a>Ponowne nadarzenie obsługi administracyjnej urządzenia

Po wprowadzeniu certyfikatu zarówno na urządzeniu, jak i w usłudze inicjowania obsługi administracyjnej urządzenia, urządzenie może ponownie aprowizować się, kontaktując się z usługą inicjowania obsługi administracyjnej urządzeń. 

Jednym z łatwych sposobów programowania urządzeń do ponownego obsługi administracyjnej jest zaprogramowanie urządzenia do kontaktu z usługą inicjowania obsługi administracyjnej, aby przejść przez przepływ inicjowania obsługi administracyjnej, jeśli urządzenie otrzyma "nieautoryzowany" błąd podczas próby połączenia się z centrum IoT hub.

Innym sposobem jest, aby stare i nowe certyfikaty były ważne dla krótkiego nakładania się i używać centrum IoT hub do wysyłania polecenia do urządzeń, aby ponownie zarejestrować je za pośrednictwem usługi inicjowania obsługi administracyjnej, aby zaktualizować informacje o połączeniu usługi IoT Hub. Ponieważ każde urządzenie może przetwarzać polecenia inaczej, trzeba będzie zaprogramować urządzenie, aby wiedzieć, co zrobić, gdy polecenie jest wywoływane. Istnieje kilka sposobów sterowania urządzeniem za pośrednictwem usługi IoT Hub i zalecamy zainicjowanie procesu przy użyciu [bezpośrednich metod](../iot-hub/iot-hub-devguide-direct-methods.md) lub [zadań.](../iot-hub/iot-hub-devguide-jobs.md)

Po zakończeniu ponownego inicjowania obsługi administracyjnej urządzenia będą mogły łączyć się z centrum IoT Hub przy użyciu nowych certyfikatów.


## <a name="blacklist-certificates"></a>Certyfikaty czarnej listy

W odpowiedzi na naruszenie zabezpieczeń może być konieczne wpisywać certyfikat urządzenia na czarną listę. Aby wpisać certyfikat urządzenia na czarną listę, wyłącz wpis rejestracji dla urządzenia docelowego/certyfikatu. Aby uzyskać więcej informacji, zobacz czarnej listy urządzeń w [zarządzaniu wyrejestrowania](how-to-revoke-device-access-portal.md) artykułu.

Gdy certyfikat zostanie uwzględniony jako część wyłączonego wpisu rejestracji, wszelkie próby zarejestrowania się w centrum IoT przy użyciu tych certyfikatów nie powiodą się, nawet jeśli jest włączony jako część innego wpisu rejestracji.
 



## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o certyfikatach X.509 w usłudze inicjowania obsługi administracyjnej urządzeń, zobacz [Zabezpieczenia](concepts-security.md) 
- Aby dowiedzieć się, jak wykonać dowód posiadania certyfikatów urzędu certyfikacji X.509 za pomocą usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub, zobacz [Jak zweryfikować certyfikaty](how-to-verify-certificates.md)
- Aby dowiedzieć się, jak utworzyć grupę rejestracji za pomocą portalu, zobacz [Zarządzanie rejestracjami urządzeń za pomocą witryny Azure Portal](how-to-manage-enrollments.md).










