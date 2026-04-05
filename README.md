# docker-dev

Ambiente Docker para projetos **PHP 8.1** com **Nginx** e **MySQL**, no estilo do repositório [flexpeak/docker-flex](https://github.com/flexpeak/docker-flex).

O código dos seus projetos fica em **`~/projetos`** (configurável no `.env`).

## Requisitos

- [Docker](https://docs.docker.com/get-docker/) e Docker Compose v2

## Instalação

1. Clone ou use esta pasta e entre nela.

2. Copie o ambiente e ajuste se precisar:

   ```bash
   cp .env.example .env
   ```

   Variáveis importantes:

   - `PROJETOS_PATH` — pasta dos projetos no host (padrão `${HOME}/projetos`)
   - `USER_UID` / `USER_GID` — devem bater com seu usuário no Linux/macOS (`id -u` / `id -g`)
   - `MYSQL_*` — credenciais e pasta de dados (`MYSQL_DATA_PATH`)

3. Suba os serviços (o primeiro `up` faz o build e pode demorar):

   ```bash
   docker compose up -d
   ```

## Uso

Execute os comandos **sempre** na pasta deste repositório (`docker-dev`).

### Shell no PHP (Composer, Artisan, npm)

```bash
docker compose exec --user=flexdock php bash
```

Como root:

```bash
docker compose exec php bash
```

### Nginx — novo site

1. Copie o modelo padrão e renomeie para um arquivo `.conf`:

   ```bash
   cp nginx/sites/projeto.conf.example nginx/sites/meuprojeto.conf
   ```

2. Edite `server_name`, `root` e os nomes dos logs no arquivo. Para Laravel (ou framework com pasta `public`), mantenha `root .../public`; para PHP na raiz do projeto, siga o comentário no próprio `.example`.

   O caminho no container é `/var/www/...`, espelhando `~/projetos` (ou o valor de `PROJETOS_PATH`).

3. Reinicie o Nginx:

   ```bash
   docker compose restart nginx
   ```

4. No host, aponte o domínio para `127.0.0.1` em `/etc/hosts` (Linux/macOS) ou `C:\Windows\System32\drivers\etc\hosts` (Windows).

### MySQL

- Host na sua máquina: `127.0.0.1` (porta padrão `3306`, ou a definida em `MYSQL_PORT`)
- Dentro da rede Docker: host `mysql`, usuário/senha do `.env`

## Estrutura

| Item        | Função                                      |
|------------|----------------------------------------------|
| `php81/`   | Imagem PHP 8.1-FPM (extensões comuns + Composer + Node 20) |
| `nginx/`   | Nginx com `sites` montados em `conf.d`       |
| `mysql/`   | Scripts opcionais em `docker-entrypoint-initdb.d` |

## Notas

- Não foram montados `/etc/timezone` e `/etc/localtime` do host para evitar erro no Docker Desktop (macOS).
- MySQL usa imagem **8.0** com `mysql_native_password` para compatibilidade ampla com clientes PHP.
