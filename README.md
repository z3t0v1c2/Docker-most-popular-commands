# Docker-most-popular-commands-in-turkish
# 🐳 Docker Komut Rehberi

> `docker run`, `docker exec`, `docker build` ve `docker ps` komutlarının, en sık kullanılan tüm seçenekleriyle birlikte kapsamlı bir referansı.

## 📑 İçindekiler

- [`docker run`](#docker-run)
- [`docker exec`](#docker-exec)
- [`docker build`](#docker-build)
- [`docker ps`](#docker-ps)
- [Hızlı Referans Tablosu](#hızlı-referans-tablosu)
- [İlgili Komutlar](#i̇lgili-komutlar)

---

## `docker run`

Belirtilen image'dan **yeni bir container oluşturur ve çalıştırır**. Var olan bir container'ı yeniden başlatmaz — bunun için `docker start` kullanılır.

### Söz Dizimi

```bash
docker run [SEÇENEKLER] IMAGE[:TAG] [KOMUT] [ARG...]
```

### Çalışma Modu

| Seçenek | Açıklama |
|---|---|
| `-d`, `--detach` | Container'ı arka planda çalıştırır, ID'yi yazdırıp terminale geri döner |
| `-i`, `--interactive` | STDIN'i açık tutar |
| `-t`, `--tty` | Sahte bir terminal (pseudo-TTY) tahsis eder |
| `-it` | `-i` ve `-t` birleşimi; interaktif shell açmak için standart kullanım |
| `--rm` | Container durunca otomatik olarak silinir (test amaçlı çalıştırmalarda disk çöpü bırakmaz) |
| `-a`, `--attach` | STDIN/STDOUT/STDERR'e bağlanır |

### İsimlendirme ve Kimlik

| Seçenek | Açıklama |
|---|---|
| `--name` | Container'a otomatik üretilen isim yerine kendi isminizi verir |
| `-h`, `--hostname` | Container içindeki hostname'i belirler |
| `-l`, `--label` | `key=value` formatında metadata ekler |

### Ağ (Network)

| Seçenek | Açıklama |
|---|---|
| `-p`, `--publish` | Host:container port eşlemesi yapar (örn. `8080:80`) |
| `-P`, `--publish-all` | Image'daki tüm `EXPOSE` edilmiş portları rastgele host portlarına açar |
| `--network` | Container'ı belirli bir ağa bağlar (`bridge`, `host`, `none` veya özel ağ adı) |
| `--add-host` | `/etc/hosts` dosyasına host:IP eşlemesi ekler |
| `--link` | (Eski/legacy) başka bir container'a bağlantı kurar; yerini custom network'ler almıştır |

### Depolama

| Seçenek | Açıklama |
|---|---|
| `-v`, `--volume` | Host dizinini veya named volume'ü container'a bağlar (`/host/yol:/container/yol`) |
| `--mount` | `-v`'nin daha açık sözdizimli hâli (`type=bind,source=...,target=...`) |

### Ortam Değişkenleri

| Seçenek | Açıklama |
|---|---|
| `-e`, `--env` | Ortam değişkeni tanımlar (`-e NODE_ENV=production`) |
| `--env-file` | Ortam değişkenlerini bir dosyadan okur |

### Kaynak Sınırlama

| Seçenek | Açıklama |
|---|---|
| `-m`, `--memory` | Bellek limiti (örn. `512m`) |
| `--cpus` | Kullanılabilecek CPU sayısı (örn. `1.5`) |
| `--gpus` | GPU erişimi verir (`--gpus all`) |

### Yetkiler ve Kullanıcı

| Seçenek | Açıklama |
|---|---|
| `-u`, `--user` | Container içinde çalıştırılacak kullanıcı/UID |
| `-w`, `--workdir` | Container içindeki çalışma dizini |
| `--privileged` | Genişletilmiş (host'a yakın) yetkiler verir — dikkatli kullanılmalı |
| `--cap-add` / `--cap-drop` | Belirli kernel capability'lerini ekler/kaldırır |

### Yeniden Başlatma ve Diğer

| Seçenek | Açıklama |
|---|---|
| `--restart` | `no`, `on-failure`, `always`, `unless-stopped` |
| `--entrypoint` | Image'ın varsayılan `ENTRYPOINT`'ini geçersiz kılar |
| `--pull` | İmajın ne zaman çekileceği: `always`, `missing`, `never` |

### Örnekler

```bash
# Nginx'i arka planda başlat, 8080 -> 80 port yönlendirmesi yap
docker run -d -p 8080:80 --name web nginx

# Ubuntu içinde interaktif bash aç, çıkınca container'ı sil
docker run -it --rm ubuntu bash

# Host dizinini container'a bağla, çalışma dizinini ayarla
docker run -v $(pwd):/app -w /app node:20 npm install

# Ortam değişkeni ver, restart policy uygula
docker run -d -e POSTGRES_PASSWORD=gizli --restart unless-stopped postgres

# GPU erişimiyle çalıştır
docker run --gpus all -it nvidia/cuda:12.4.1-base-ubuntu24.04 nvidia-smi
```

---

## `docker exec`

**Zaten çalışmakta olan** bir container'ın içinde yeni bir komut çalıştırır. `docker run` yeni container oluştururken, `exec` var olana "girer".

### Söz Dizimi

```bash
docker exec [SEÇENEKLER] CONTAINER KOMUT [ARG...]
```

### Seçenekler

| Seçenek | Açıklama |
|---|---|
| `-i`, `--interactive` | STDIN'i açık tutar |
| `-t`, `--tty` | Pseudo-TTY tahsis eder |
| `-it` | İkisi birlikte; container içine shell ile girmenin standart yolu |
| `-d`, `--detach` | Komutu arka planda çalıştırır |
| `-u`, `--user` | Komutu belirli bir kullanıcı/UID ile çalıştırır |
| `-w`, `--workdir` | Komutun çalışacağı dizini belirler |
| `-e`, `--env` | Ek ortam değişkeni tanımlar |
| `--env-file` | Ortam değişkenlerini dosyadan okur |
| `--privileged` | Genişletilmiş yetkilerle çalıştırır |
| `--detach-keys` | Detach (ayrılma) tuş kombinasyonunu özelleştirir |

### Örnekler

```bash
# Çalışan bir container'a interaktif shell ile gir
docker exec -it web bash

# Root yerine belirli bir kullanıcı ile komut çalıştır
docker exec -u 1000 -it web sh

# Belirli bir dizinde komut çalıştır
docker exec -w /var/www web ls -la

# Arka planda tek seferlik komut çalıştır
docker exec -d web touch /tmp/hazir.txt
```

> 💡 **Not:** `docker exec`, container'ın **çalışıyor** olmasını gerektirir. Durmuş bir container'a `exec` ile giremezsiniz; önce `docker start` ile başlatmanız gerekir.

---

## `docker build`

Bir `Dockerfile`'dan yeni bir **image** üretir.

### Söz Dizimi

```bash
docker build [SEÇENEKLER] YOL | URL | -
```

### Temel Seçenekler

| Seçenek | Açıklama |
|---|---|
| `-t`, `--tag` | İmaja isim ve tag verir (`-t app:1.0`) — birden fazla kez kullanılabilir |
| `-f`, `--file` | Varsayılan `Dockerfile` yerine farklı bir dosya belirtir |

### Cache ve Build Davranışı

| Seçenek | Açıklama |
|---|---|
| `--no-cache` | Build cache'ini kullanmadan sıfırdan build eder |
| `--pull` | Yerelde güncel olsa bile base image'ı yeniden çeker |
| `--build-arg` | Dockerfile'daki `ARG` değerlerini build zamanında belirler |
| `--target` | Multi-stage Dockerfile'larda hangi stage'in build edileceğini belirtir |
| `--progress` | İlerleme çıktısının biçimi: `auto`, `plain`, `tty` |

### BuildKit / buildx Tabanlı Seçenekler

Güncel Docker sürümlerinde (BuildKit varsayılan builder) doğrudan kullanılabilir:

| Seçenek | Açıklama |
|---|---|
| `--platform` | Hedef platform(lar) (`linux/amd64,linux/arm64`) |
| `-o`, `--output` | Build çıktısının nereye yazılacağı (yerel dosya sistemi, tar vb.) |
| `--cache-from` / `--cache-to` | Uzak/yerel build cache kaynakları |
| `--secret` | Build sürecine imaja gömülmeden secret aktarır |
| `--ssh` | SSH agent soketi/anahtarlarını build'e taşır (özel repo clone'ları için) |

### Diğer

| Seçenek | Açıklama |
|---|---|
| `--network` | `RUN` adımlarının kullanacağı network modu |
| `--label` | İmaja metadata ekler |
| `--add-host` | Build sırasında `/etc/hosts`'a giriş ekler |

### Örnekler

```bash
# Basit build, mevcut dizindeki Dockerfile'ı kullan
docker build -t benimuygulamam:1.0 .

# Farklı bir Dockerfile ile build
docker build -f Dockerfile.prod -t benimuygulamam:prod .

# Cache kullanmadan, build-arg ile
docker build --no-cache --build-arg NODE_ENV=production -t app:latest .

# Multi-stage Dockerfile'da sadece "test" stage'ini build et
docker build --target test -t app:test .

# Çoklu mimari için build ve registry'ye push
docker buildx build --platform linux/amd64,linux/arm64 -t kullanici/app:1.0 --push .
```

> 💡 **Not:** Birden fazla mimari için build ederken genellikle `docker buildx build` kullanılır ve sonuç doğrudan `--push` ile bir registry'ye gönderilir; çünkü tek bir yerel image olarak birden fazla mimariyi aynı anda tutmak mümkün değildir.

---

## `docker ps`

Çalışan (veya `-a` ile tüm) container'ları listeler.

### Söz Dizimi

```bash
docker ps [SEÇENEKLER]
```

### Seçenekler

| Seçenek | Açıklama |
|---|---|
| `-a`, `--all` | Sadece çalışanları değil, durmuş container'ları da gösterir |
| `-q`, `--quiet` | Sadece container ID'lerini yazdırır (script'ler için idealdir) |
| `-f`, `--filter` | Koşula göre filtreler (`status=running`, `name=web` vb.) |
| `-n`, `--last` | Son oluşturulan N container'ı gösterir (durmuşlar dahil) |
| `-l`, `--latest` | En son oluşturulan container'ı gösterir |
| `-s`, `--size` | Container'ların disk boyutunu da gösterir |
| `--no-trunc` | Çıktıyı kısaltmadan (ID, komut vb. tam haliyle) gösterir |
| `--format` | Çıktıyı Go template sözdizimiyle özelleştirir |

### Örnekler

```bash
# Sadece çalışan container'lar
docker ps

# Tüm container'lar (durmuş dahil)
docker ps -a

# Sadece ID'leri listele
docker ps -aq

# İsme göre filtrele
docker ps -f name=web

# Duruma göre filtrele (örn. exited olanlar)
docker ps -a -f status=exited

# Özel tablo formatıyla göster
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"

# Son oluşturulan 3 container
docker ps -n 3
```

---

## Hızlı Referans Tablosu

| Komut | Ne İşe Yarar | En Sık Kullanılan Hâli |
|---|---|---|
| `docker run` | Yeni container oluşturup çalıştırır | `docker run -it --rm image bash` |
| `docker exec` | Çalışan container'a komut gönderir/girer | `docker exec -it container bash` |
| `docker build` | Dockerfile'dan image üretir | `docker build -t isim:tag .` |
| `docker ps` | Container'ları listeler | `docker ps -a` |

---

## İlgili Komutlar

`run` / `exec` / `build` / `ps` ile birlikte sık kullanılan birkaç komut:

```bash
docker logs -f web       # Logları canlı takip et
docker stop web          # Container'ı durdur
docker start web         # Durmuş container'ı yeniden başlat
docker rm web            # Durmuş container'ı sil
docker images            # Yerel image'ları listele
docker rmi image:tag     # Image'ı sil
```

---

<p align="center"><i>🐳 Docker ile keyifli çalışmalar!</i></p>
