# Gestão de Escalas — login corporativo

## Como acessar

1. Abra a aplicação e clique **Entrar com conta Manus**.
2. Autentique-se em uma conta Manus associada ao seu e-mail **@fielturismo.com.br**. O aplicativo não tem senha própria de e-mail/senha: o provedor realiza o login. Se estiver usando uma conta pessoal, troque para a conta corporativa no provedor.
3. No primeiro acesso de um colaborador, aparecerá **Seu acesso aguarda liberação**. O administrador precisa liberar o perfil em **Usuários e perfis**.
4. Após a liberação, clique **Verificar liberação** ou recarregue a página. Os módulos ficam disponíveis conforme o perfil.

A conta administradora inicial é o e-mail corporativo explicitamente configurado pelo proprietário, não o primeiro visitante. Esse e-mail já está configurado e pertence ao domínio autorizado. O usuário dessa conta precisa efetuar seu primeiro login para o perfil ser registrado no banco; nenhuma conta foi criada ou liberada automaticamente durante os testes.

## Quem pode entrar

| Conta                                                            | Resultado                              |
| ---------------------------------------------------------------- | -------------------------------------- |
| Colaborador com `nome@fielturismo.com.br`, já liberado           | Acesso conforme seu perfil             |
| Colaborador com domínio corporativo, ainda não liberado          | Tela de espera; sem acesso aos módulos |
| E-mail de outro domínio, inclusive conta com perfil Admin antigo | Bloqueado                              |
| Endereço de subdomínio ou domínio parecido                       | Bloqueado                              |
| Identidade sem e-mail ou endereço inválido                       | Bloqueado                              |
| Pessoa sem login                                                 | Apenas a tela de entrada               |

O domínio é comparado sem diferenciar maiúsculas/minúsculas. A demonstração também exige login corporativo e admissão; não serve para contornar a restrição.

## Liberação pela administração

O administrador abre **Usuários e perfis** e escolhe **Consulta** ou **Operador** para os colaboradores corporativos pendentes. A alteração é auditada. Identidades externas antigas permanecem registradas, sem exclusão, mas aparecem bloqueadas por domínio e não podem ser liberadas.

| Perfil   | Permissões                                                                         |
| -------- | ---------------------------------------------------------------------------------- |
| Admin    | Gerencia a equipe e todos os módulos                                               |
| Operador | Registra, edita, confirma e replica saídas; acompanha alertas e exporta relatórios |
| Consulta | Visualiza registros, alertas e relatórios, sem alterar ou replicar                 |

## Proteção técnica

A restrição não depende somente de esconder botões. O callback do OAuth valida o e-mail retornado pelo provedor antes de registrar a identidade ou emitir a sessão. O backend verifica assinatura/expiração/projeto do JWT, consulta a identidade atual no provedor, confere a vinculação ao usuário e valida o domínio em cada requisição autenticada. O projeto está vinculado ao JWT assinado, à troca do código OAuth e à requisição de identidade. Quando a resposta também inclui `projectId`, ele precisa corresponder ao projeto; não exigimos um campo ausente do contrato atual do provedor. APIs REST e tRPC, inclusive gerenciamento de perfis e replicação em lote, exigem a política corporativa. Claims de e-mail arbitrários no JWT, campos do formulário ou perfil Admin não criam exceção.

O contrato do provedor não expõe um campo `emailVerified`; não afirmamos possuir essa informação. Usamos a identidade fornecida pelo provedor após autenticação, não um endereço digitado no aplicativo. Se o provedor estiver indisponível, o servidor não usa um e-mail antigo do banco para liberar acesso: os módulos online ficam bloqueados e a tela oferece nova tentativa, mas essa falha temporária não apaga a cópia corporativa previamente autorizada. A atribuição inicial de Admin usa transação e bloqueio de banco para impedir duas promoções concorrentes pelo mesmo e-mail configurado.

A tela revalida a sessão ao receber foco e a cada 30 segundos enquanto conectada. Se a API rejeitar uma sessão ou perfil durante o uso, os módulos são fechados e os dados locais são removidos. A consulta offline é limitada à cópia previamente carregada por uma identidade corporativa admitida no mesmo dispositivo; sem internet não é possível verificar mudanças posteriores de e-mail/perfil no provedor. Logout remove essa cópia imediatamente. Esta cópia não é armazenamento criptografado e não substitui a proteção do dispositivo.

## O que permanece igual

A alteração não apaga usuários, perfis, escalas ou lotes existentes. Campos manuais, status escolhido pelo operador, alertas laranja/vermelho/roxo e replicação que muda somente datas são preservados. Não foi alterado o provedor de login nem iniciado novo deploy público; a versão atual fica disponível na prévia do projeto.
