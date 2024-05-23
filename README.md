# Sistema Medical System em Laravels

## Introdução

Sistema de agendamento de exames de uma clinica médica.


## Ações disponiveis

Cadastro de Clientes;
Cadastro de Médicos;
Cadastro de Funcionários;
Agendar exame;
Realização do exame;
Informações pré exame;
Verificação de taxa;
Visualização do laudo;
Retirada do exame;
Geração de relatórios;
Atualização do prontuário;
Consulta do histórico dos pacientes (prontuário);

## Instalação

Em uma pasta do seu computador, digitar no cmd =  git clone https://github.com/vongrafen/medicalSystem

OBS: "Irá fazer dowload do projeto"

DENTRO DA PASTA "medicalsystem" digitar = composer install
OBS: "Irá criar as pastas necessárias"

Se não tiver o banco de dados, Criar no mysql

Abrir o arquivo .env.example e substituir as linhas : DB_DATABASE=SUA_BASE_DE_DADOS /// DB_USERNAME=SEU_NOME_DE_USUARIO /// DB_PASSWORD=SUA_SENHA

Rodar o comando = php -r "copy('.env.example', '.env');"
Rodar o comando = php artisan key:generate
Rodar o comando = php artisan migrate --seed
Rodar o comando = php artisan serve

Para logar-se como ADMIN, acesse a url http://SEU_LOCAL_HOST/home

Login e senha padrão: 
*login:* admin 
*senha:*admin


## License

The Laravel framework is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT).
