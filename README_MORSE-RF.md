# MORSE-RF
### Rede de comunicação descentralizada para ambientes sem infraestrutura

> **Status:** Projeto em fase de concepção e prototipagem  
> **Plataforma inicial planejada:** ESP32 + transceptor de rádio compatível  
> **Idioma principal da documentação:** Português (Brasil)

O **MORSE-RF** é um projeto open source que busca desenvolver uma rede de mensagens digitais resiliente, reparável e energeticamente eficiente, capaz de funcionar sem internet, operadoras de telefonia ou servidores centralizados.

Inspirado em cenários de perda prolongada de infraestrutura — como os retratados em *The Last of Us* —, o projeto pretende ajudar pessoas e comunidades a trocar mensagens quando as redes convencionais estiverem indisponíveis.

**Importante:** o projeto ainda está em desenvolvimento. Recursos descritos como objetivos não devem ser interpretados como implementados, testados ou garantidos.

---

## Índice

- [Visão](#visão)
- [Objetivos](#objetivos)
- [O que o projeto não promete](#o-que-o-projeto-não-promete)
- [Arquitetura proposta](#arquitetura-proposta)
- [Tecnologias previstas](#tecnologias-previstas)
- [Protocolo de mensagens](#protocolo-de-mensagens)
- [Plano de desenvolvimento](#plano-de-desenvolvimento)
- [Requisitos de aceitação](#requisitos-de-aceitação)
- [Energia e manutenção](#energia-e-manutenção)
- [Segurança e privacidade](#segurança-e-privacidade)
- [Uso de radiofrequência no Brasil](#uso-de-radiofrequência-no-brasil)
- [Como contribuir](#como-contribuir)
- [Licenciamento](#licenciamento)
- [Próximos passos](#próximos-passos)

## Visão

Criar uma plataforma de comunicação comunitária que continue útil quando a infraestrutura comercial estiver indisponível. O sistema deverá permitir mensagens entre dispositivos próximos e, quando houver nós intermediários e enlaces viáveis, encaminhar mensagens entre locais mais distantes.

A prioridade é equilibrar:

1. **Resiliência:** lidar com perda de pacotes, interrupções e nós indisponíveis.
2. **Autonomia:** funcionar com baterias e, em estações fixas, permitir alimentação solar.
3. **Reparabilidade:** usar módulos substituíveis, documentação clara e pontos de teste.
4. **Privacidade:** proteger o conteúdo das mensagens e reduzir a coleta de dados desnecessários.
5. **Acessibilidade:** oferecer uma interface simples e documentação em português.

## Objetivos

- Trocar mensagens de texto sem internet ou serviços centralizados.
- Confirmar a integridade e a entrega das mensagens.
- Armazenar mensagens localmente quando o destinatário estiver indisponível.
- Encaminhar mensagens entre nós, com limites para evitar ciclos e duplicatas.
- Medir consumo, confiabilidade e alcance em testes documentados.
- Facilitar diagnóstico, reparo e substituição de componentes.
- Investigar modos de entrada e sinalização inspirados no código Morse.
- Cumprir as regras aplicáveis de uso de radiofrequência e homologação.

## O que o projeto não promete

- **Não promete comunicação indetectável.** Qualquer transmissão de rádio pode ser detectada por equipamentos adequados.
- **Não garante um alcance específico.** O alcance depende de frequência, antenas, relevo, obstáculos, potência autorizada, interferência e sensibilidade do receptor.
- **Não garante funcionamento em qualquer emergência.** O sistema precisará ser testado e mantido; não substitui meios oficiais de emergência.
- **Não considera criptografia uma forma de ocultar a presença de uma transmissão.** Ela protege o conteúdo, mas não elimina metadados ou emissões de RF.
- **Não está pronto para uso crítico.** Até haver testes e revisão independentes, deve ser considerado experimental.

## Arquitetura proposta

O projeto prevê três funções lógicas. Elas poderão usar a mesma plataforma de hardware, dependendo da configuração.

### 1. Nó portátil

- Interface de texto por teclado, botões ou dispositivo conectado.
- Visor opcional.
- Rádio, antena compatível e bateria protegida.
- Fila local de mensagens e indicador de entrega.
- Modo de baixo consumo quando suportado pelo hardware e protocolo.

### 2. Nó comunitário

- Recebe, valida e armazena mensagens.
- Encaminha mensagens quando há um enlace disponível.
- Pode funcionar com bateria e sistema solar dimensionados a partir de medições reais.
- Deve continuar operando sem conexão a servidores externos.

### 3. Nó de ligação

- Estabelece enlaces entre comunidades quando as condições de rádio permitem.
- Pode usar antena externa apropriada e alimentação independente.
- Deve respeitar as condições regulamentares aplicáveis ao equipamento e à operação.

### Fluxo lógico

```text
Interface de texto
       |
       v
Codificação e endereçamento
       |
       v
Identificador + sequência + limite de saltos
       |
       v
Verificação de integridade e proteção criptográfica
       |
       v
Camada de rádio
       |
       v
Recepção -> validação -> armazenamento
       |
       v
Confirmação de entrega / encaminhamento
```

A camada de mensagens deverá ser separada da camada de rádio para permitir, no futuro, avaliar outros transceptores sem reescrever toda a aplicação.

## Tecnologias previstas

| Área | Opção inicial | Observações |
|---|---|---|
| Microcontrolador | ESP32 | Plataforma inicial de desenvolvimento; consumo depende da placa e da configuração |
| Rádio | Transceptor compatível, por exemplo baseado em SX1262 | A versão exata deve ser adequada à faixa autorizada e à aplicação |
| Modulação inicial | LoRa, se apropriada e permitida | Boa candidata para mensagens de baixa taxa de dados; não garante alcance |
| Interface | OLED e botões ou teclado | Pode ser simplificada para economizar energia |
| Armazenamento | Memória não volátil disponível ou componente dedicado | Avaliar capacidade, desgaste e recuperação após falha de energia |
| Energia | Bateria protegida; solar para estações | Dimensionar após medir o consumo real |
| Firmware | C/C++ com ambiente compatível com a placa | A ferramenta definitiva será escolhida durante o protótipo |

A seleção de módulos, antenas, potência, frequência e parâmetros de transmissão só deverá ser finalizada após verificar as regras brasileiras aplicáveis e a homologação do equipamento.

## Protocolo de mensagens

O formato definitivo ainda será especificado. A proposta inicial considera os seguintes campos:

| Campo | Finalidade |
|---|---|
| Versão | Permitir evolução compatível do protocolo |
| ID da mensagem | Identificar unicamente uma mensagem |
| Origem e destino | Direcionar a mensagem |
| Número de sequência | Identificar ordem e duplicatas |
| Limite de saltos | Evitar encaminhamento indefinido |
| Dados | Conteúdo da mensagem |
| Integridade | Detectar corrupção |
| Autenticação | Verificar origem e integridade criptográfica, quando habilitada |

### Comportamento esperado

1. O emissor cria a mensagem e a guarda localmente.
2. A mensagem é enviada como pacote de rádio.
3. O receptor valida a integridade e, quando configurado, a autenticação.
4. O receptor guarda a mensagem antes de confirmar o recebimento.
5. O emissor repete o envio de forma limitada se não receber confirmação.
6. Os nós intermediários evitam duplicatas, aplicam limites de encaminhamento e descartam mensagens expiradas segundo regras documentadas.

O **CRC** ajuda a detectar erros acidentais, mas não é mecanismo de segurança. Autenticação e criptografia precisam de desenho separado, gerenciamento adequado de chaves e revisão de segurança.

### Papel do Morse

O Morse poderá ser uma opção de entrada manual ou um modo alternativo de sinalização. Na primeira versão, a prioridade é validar o transporte digital de mensagens. Não se deve presumir que usar Morse torna uma transmissão menos detectável ou mais segura.

## Plano de desenvolvimento

### Fase 0 — Especificação e simulação

- Documentar o formato inicial de mensagem.
- Criar codificador e decodificador de texto.
- Implementar identificadores, sequências e tratamento de duplicatas.
- Testar armazenamento local e recuperação após reinicialização.
- Simular perda, atraso e duplicação de pacotes sem transmitir RF.

### Fase 1 — Dois nós

- Integrar dois microcontroladores e transceptores adequados.
- Enviar e receber mensagens curtas.
- Implementar verificação de integridade e confirmação de recebimento.
- Registrar erros e comportamento após falha de energia.

### Fase 2 — Mensagens persistentes

- Guardar mensagens destinadas a aparelhos indisponíveis.
- Implementar expiração, limites de armazenamento e política de descarte.
- Verificar que mensagens confirmadas não sejam apresentadas repetidamente.

### Fase 3 — Três nós e encaminhamento

- Introduzir um nó intermediário.
- Testar rotas interrompidas e recuperação de enlaces.
- Evitar ciclos, tempestades de retransmissão e duplicatas.
- Medir atraso e taxa de entrega.

### Fase 4 — Autonomia e testes de campo

- Medir consumo em espera, recepção e transmissão.
- Estimar autonomia usando dados medidos.
- Testar em diferentes ambientes e documentar condições.
- Operar apenas com equipamentos e parâmetros autorizados.

### Fase 5 — Revisão de segurança e documentação

- Revisar autenticação, criptografia e gestão de chaves.
- Testar atualizações e recuperação do firmware.
- Publicar resultados, limitações e instruções de manutenção.
- Só então avaliar uso em cenários mais exigentes.

## Requisitos de aceitação

As metas abaixo são critérios propostos para os testes, não resultados já obtidos.

- [ ] Enviar 100 mensagens de teste em bancada e verificar corretamente o conteúdo recebido.
- [ ] Detectar pacotes corrompidos nos testes de integridade.
- [ ] Confirmar recebimento e limitar retransmissões.
- [ ] Preservar mensagens confirmadas após reinicializações controladas.
- [ ] Encaminhar mensagens por um terceiro nó sem duplicação visível ao usuário.
- [ ] Evitar ciclos de encaminhamento com limites explícitos.
- [ ] Registrar consumo e autonomia medidos.
- [ ] Documentar as condições de cada teste de alcance.
- [ ] Documentar procedimento de atualização, recuperação e substituição de componentes.
- [ ] Verificar os requisitos regulamentares antes de testes de transmissão.

## Energia e manutenção

A autonomia deve ser calculada a partir de medições. Como aproximação:

```text
autonomia (horas) ≈ energia utilizável da bateria (Wh)
                    / potência média (W)
```

A capacidade nominal da bateria não é inteiramente utilizável e o consumo varia com o ciclo de recepção, a transmissão, o visor e os reguladores.

Princípios de manutenção:

- Usar conectores e módulos substituíveis quando possível.
- Disponibilizar pontos de teste e instruções de diagnóstico.
- Documentar pinagem, tensões e consumo esperado.
- Proteger baterias contra curto-circuito, sobrecarga e descarga excessiva.
- Evitar afirmar autonomia ou alcance sem testes reproduzíveis.

## Segurança e privacidade

A segurança será um requisito de projeto, não um recurso adicionado apenas no fim.

- Usar criptografia autenticada com bibliotecas reconhecidas, quando o desenho e a plataforma estiverem definidos.
- Não inventar algoritmos criptográficos próprios.
- Proteger chaves e documentar o processo de provisionamento e recuperação.
- Tratar o CRC como detecção de erro, não como autenticação.
- Considerar repetição de mensagens, falsificação de origem, esgotamento de bateria e armazenamento malicioso.
- Minimizar metadados e evitar dados pessoais desnecessários.
- Documentar que transmissões podem ser detectadas e que o conteúdo criptografado não oculta necessariamente horário, duração ou origem aproximada.
- Não usar o sistema experimental como único meio de comunicação em situações de risco à vida.

## Uso de radiofrequência no Brasil

Antes de transmitir, verificar a regulamentação vigente da Anatel para a faixa, o equipamento, a potência, a largura de banda, a antena e a aplicação pretendida.

- Não presumir que uma frequência é livre apenas por ser usada por módulos de baixo custo.
- Verificar a homologação aplicável ao modelo exato e ao equipamento final.
- Não modificar potência ou antena sem avaliar os requisitos regulamentares.
- Para radioamadorismo, verificar os requisitos específicos do serviço e da operação.
- Documentar o módulo e a configuração usados em cada teste.

Referências oficiais para começar:

- [Anatel — Radiação restrita](https://www.gov.br/anatel/pt-br/regulado/radiofrequencia/radiacao-restrita)
- [Anatel — Certificação e homologação](https://www.gov.br/anatel/pt-br/regulado/certificacao-de-produtos)

Este documento não substitui a verificação da regulamentação vigente nem constitui autorização para transmitir.

## Como contribuir

Contribuições são bem-vindas em documentação, firmware, testes, esquemas, eficiência energética, acessibilidade e segurança.

Antes de abrir uma contribuição:

1. Descreva o problema ou a melhoria.
2. Explique como reproduzir o problema, quando aplicável.
3. Inclua resultados de testes e informações da placa utilizada.
4. Evite publicar chaves privadas, credenciais ou dados pessoais.
5. Diferencie claramente resultados medidos de hipóteses.

Contribuições que alterem o protocolo devem documentar compatibilidade, impacto na segurança e comportamento com versões anteriores.

## Licenciamento

A licença ainda precisa ser escolhida antes da primeira publicação.

Opções a avaliar:
- **Firmware:** MIT para permissões amplas ou GPL-3.0 para exigir compartilhamento sob a mesma licença em distribuições cobertas pela licença.
- **Hardware:** CERN-OHL-S-2.0 para projetos de hardware aberto com reciprocidade.

A licença escolhida deve ser incluída no repositório e aplicada de forma consistente aos arquivos correspondentes. Não declare uma licença até que a decisão esteja tomada.

## Próximos passos

1. Criar o repositório e publicar esta especificação.
2. Definir a licença e as regras de contribuição.
3. Implementar o codificador de mensagens e os testes locais.
4. Selecionar a placa e o transceptor exatos após verificar disponibilidade e requisitos regulatórios.
5. Construir dois nós de bancada e registrar os primeiros resultados.

---

**MORSE-RF — comunicação resiliente, conhecimento aberto e tecnologia reparável.**

*Estado atual: proposta técnica. Hardware, firmware, alcance, autonomia e segurança ainda precisam ser implementados e validados.*
