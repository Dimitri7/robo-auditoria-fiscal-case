# 🧾 Robô de Auditoria Fiscal — estudo de caso

> Ferramenta que lê a planilha de entradas fiscais de uma empresa, extrai todos os CNPJs de fornecedores e consulta cada um em dados abertos da Receita Federal, gerando um relatório de auditoria pronto em Excel.
> **Projeto real, desenvolvido sob encomenda para um cliente da área contábil.**

*Este repositório contém apenas a descrição do projeto. O código-fonte pertence ao cliente e não é publicado aqui, e nenhum dado do cliente ou de empresas auditadas é exibido.*

---

## 🧩 O problema

Na auditoria de entradas fiscais, o contador precisa saber, para **cada fornecedor** da planilha, se ele é **optante pelo Simples Nacional** e qual é a sua **situação cadastral**. Isso muda o tratamento fiscal (por exemplo, o direito a crédito de ICMS).

Feito à mão, o trabalho é lento e repetitivo:

- copiar CNPJ por CNPJ da planilha exportada pelo sistema fiscal;
- consultar um a um nos portais do governo;
- anotar o resultado e montar o relatório.

Uma planilha de um único mês pode ter centenas de linhas, com o mesmo fornecedor aparecendo várias vezes.

## 💡 A solução

Um robô em Python que faz o processo inteiro, do arquivo ao relatório, **sem intervenção humana, sem navegador e sem custo**:

1. **Lê a planilha** (`.xls`, `.xlsx` ou `.csv`) e identifica a empresa auditada pelo cabeçalho e os fornecedores no corpo;
2. **Valida cada CNPJ** pelos dígitos verificadores e **remove duplicatas** (uma planilha de exemplo com 150 linhas tinha só 3 CNPJs distintos);
3. **Consulta dados abertos da Receita Federal**: opção pelo Simples Nacional, MEI, situação cadastral, porte, CNAE e endereço;
4. **Gera o relatório** em Excel (com cores por parecer, filtros e aba de resumo), CSV e JSON, além de um PDF-resumo por CNPJ.

Pode ser usado por **interface gráfica** (abrir arquivo → iniciar → abrir relatório) ou por **linha de comando**, no Windows e no macOS, com instaladores de um clique.

## 🛠️ Stack

**Python** · requests · pandas · openpyxl / xlrd (planilhas) · CustomTkinter (interface) · unittest

## ⚙️ Decisões de engenharia

| Desafio | Como resolvi |
|---|---|
| Fontes de dados gratuitas às vezes caem ou limitam requisições | **Cascata de 3 fontes** de dados abertos: se uma falha, está fora do ar ou devolve erro, o robô passa para a próxima automaticamente. O relatório registra qual fonte respondeu. |
| Limite de requisições (HTTP 429) | **Retentativa com espera crescente** e pausa entre consultas, para não sobrecarregar os serviços. |
| "Não optante" tem dois significados diferentes | A tabela do Simples só tem linha para quem **já optou alguma vez**. O robô distingue *"excluída do Simples em tal data"* de *"nunca foi optante"*, distinção que importa numa auditoria. |
| CNPJs digitados errados ou repetidos | Validação pelos **dígitos verificadores** antes de qualquer consulta e deduplicação. |
| Planilhas em formatos diferentes | Leitura de `.xls`, `.xlsx` e `.csv`; se não houver cabeçalho, o primeiro CNPJ válido vira a empresa auditada. |
| Usuário final não técnico | Instaladores e atalhos de duplo clique para Windows e macOS, interface gráfica e relatório em Excel com **parecer colorido** (regular, atenção, irregular). |
| Confiabilidade | **21 testes automatizados** cobrindo validação de CNPJ, leitura de planilhas, cascata entre fontes (fonte fora do ar, exceção de rede), interpretação do Simples e geração dos arquivos de saída. Nenhum teste depende da internet. |

## 🔒 Uma decisão consciente: a Certidão Negativa

O cliente também queria emitir a **Certidão Negativa de Débitos** de cada CNPJ. O site oficial que emite a certidão é protegido por **hCaptcha**, um controle que existe justamente para impedir emissão automatizada em massa.

O motor de dados abertos **não contorna esse CAPTCHA**. Em vez disso, a documentação entregue ao cliente apresenta os caminhos legítimos para automatizar essa etapa:

- **e-CAC com certificado digital**, que o escritório contábil já possui e onde a emissão é autenticada;
- **API oficial do SERPRO** (Integra Contador), para automação em volume;
- **emissão manual pontual**, com o link de cada CNPJ já pronto no relatório.

## 📈 Resultado

- O que levava horas de consulta manual passa a ser **um clique**: a planilha entra e o relatório sai.
- Relatório padronizado, com o parecer de cada fornecedor e a fonte de cada informação.
- Entregue com instaladores, manual de uso e guia de solução de problemas.

## 🧠 O que aprendi

- Integração resiliente com APIs de terceiros (fallback, retentativas, limites de requisição);
- Regras de negócio fiscais reais e a importância de entender o domínio antes de codar;
- Testes automatizados para dar confiança em mudanças;
- Empacotar uma ferramenta Python para usuários não técnicos em dois sistemas operacionais;
- Saber dizer **não** a uma automação que contornaria um controle de segurança, e oferecer alternativas.

## 🤝 Como foi desenvolvido

Projeto desenvolvido em parceria com o **Claude Code** (IA da Anthropic) como par de programação: eu conduzi os requisitos com o cliente, as decisões de produto e a validação com dados reais; a IA acelerou a implementação, os testes e a documentação.

---

👤 **Dimitri Gomes de Albuquerque** · [GitHub](https://github.com/Dimitri7) · [LinkedIn](https://www.linkedin.com/in/dimitri-albuquerque-66b32b278/)
