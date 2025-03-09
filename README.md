# Firewall-bungen# Übungen zur Firewall-Konfiguration mit `iptables`

In diesen Übungen werden wir verschiedene Regeln für die Firewall-Konfiguration mit `iptables` erstellen, um den Netzwerkverkehr zu steuern. Wir beginnen mit einem einfachen Webserver, blockieren dann den Zugriff auf bestimmte Ports und passen die Regeln an, um den Verkehr nach verschiedenen Kriterien zu steuern.

## 1. Webserver auf TCP/8080 starten
Zunächst starten wir einen einfachen Webserver auf Port 8080, um den eingehenden Verkehr zu simulieren.

### Befehl:
```bash
python3 -m http.server 8080
2. Eingehenden Traffic auf TCP/8080 blockieren
```
Nun blockieren wir den eingehenden Traffic auf Port 8080, um sicherzustellen, dass niemand mehr auf den Webserver zugreifen kann.
Befehl:

sudo iptables -A INPUT -p tcp --dport 8080 -j DROP

Erklärung:

    -A INPUT: Fügt die Regel zur INPUT-Kette hinzu.
    -p tcp: Beschränkt die Regel auf TCP-Verkehr.
    --dport 8080: Beschränkt die Regel auf Port 8080.
    -j DROP: Blockiert den Verkehr.

Ergebnis: Nach dem Ausführen dieser Regel wird der Webserver nicht mehr über http://localhost:8080 erreichbar sein.
3. Überprüfung der Blockierung

Nachdem wir den Verkehr blockiert haben, testen wir, ob der Webserver noch erreichbar ist.
Befehl:

curl http://localhost:8080

Wenn der Webserver blockiert wurde, sollte die Anfrage nicht beantwortet werden.
4. Zugriff auf TCP/8080 wieder erlauben

Um den Zugriff auf den Webserver wieder zu ermöglichen, entfernen wir die Blockierungsregel und erlauben den Zugriff auf Port 8080.
Befehl:

sudo iptables -D INPUT -p tcp --dport 8080 -j DROP
sudo iptables -A INPUT -p tcp --dport 8080 -j ACCEPT

Erklärung:

    -D INPUT: Löscht die Regel, die den Verkehr blockiert hat.
    -A INPUT -p tcp --dport 8080 -j ACCEPT: Erlaubt den eingehenden TCP-Verkehr auf Port 8080.

Ergebnis: Jetzt sollte der Webserver wieder zugänglich sein.
5. Traffic von einer bestimmten IP blockieren

Nun blockieren wir den Zugriff von einer bestimmten IP-Adresse, z. B. 192.168.1.100, auf den Webserver.
Befehl:

sudo iptables -A INPUT -s 192.168.1.100 -j DROP

Erklärung:

    -s 192.168.1.100: Beschränkt die Regel auf die IP-Adresse 192.168.1.100.
    -j DROP: Blockiert den Verkehr von dieser IP-Adresse.

Ergebnis: Alle Verbindungen von 192.168.1.100 werden blockiert.
6. Traffic von einer bestimmten IP erlauben

Wir erlauben nun nur der IP-Adresse 192.168.1.100 den Zugriff auf den Webserver, während alle anderen IP-Adressen blockiert werden.
Befehl:

sudo iptables -A INPUT -s 192.168.1.100 -j ACCEPT

Erklärung:

    Nur die IP 192.168.1.100 darf auf den Webserver zugreifen.

Ergebnis: Nun haben nur noch Verbindungen von 192.168.1.100 Zugriff auf den Webserver.
7. Traffic basierend auf Protokoll blockieren

In dieser Übung blockieren wir den ICMP-Verkehr (Ping-Anfragen), um sicherzustellen, dass niemand unser System anpingen kann.
Befehl:

sudo iptables -A INPUT -p icmp -j DROP

Erklärung:

    -p icmp: Blockiert ICMP (Ping) Anfragen.
    -j DROP: Verwirft die Pakete.

Ergebnis: Nun kann niemand mehr Pings an das System senden.
8. Traffic basierend auf Protokoll erlauben

Nun erlauben wir nur SSH-Verbindungen auf Port 22, um sicherzustellen, dass nur diese Art von Verbindungen zugelassen wird.
Befehl:

sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

Erklärung:

    --dport 22: Erlaubt den Verkehr nur auf Port 22 (SSH).
    -j ACCEPT: Erlaubt den Verkehr.

Ergebnis: Nur SSH-Verbindungen auf Port 22 sind jetzt erlaubt.
9. Neue Kette erstellen

Wir erstellen eine benutzerdefinierte Kette namens MYCHAIN, um zusätzliche Regeln hinzuzufügen.
Befehl:

sudo iptables -N MYCHAIN

Erklärung:

    -N MYCHAIN: Erstellt eine neue Kette mit dem Namen MYCHAIN.

Ergebnis: Die benutzerdefinierte Kette MYCHAIN ist nun verfügbar.
10. Traffic an eine Kette weiterleiten

Nun leiten wir den Verkehr für Port 8080 an die benutzerdefinierte Kette MYCHAIN weiter.
Befehl:

sudo iptables -A INPUT -p tcp --dport 8080 -j MYCHAIN

Erklärung:

    -j MYCHAIN: Leitet den Verkehr auf Port 8080 an die Kette MYCHAIN weiter.

Ergebnis: Der Verkehr auf Port 8080 wird nun an MYCHAIN weitergeleitet.
11. Eine bestimmte Regel löschen

Um eine Regel zu löschen, z. B. die Regel #3 in der INPUT-Kette, verwenden wir die folgenden Befehle:
Befehl:

sudo iptables -L --line-numbers
sudo iptables -D INPUT 3

Erklärung:

    -L --line-numbers: Listet alle Regeln mit Nummern auf.
    -D INPUT 3: Löscht die Regel an der Position 3 der INPUT-Kette.

