# 🕵️ Detetive dos Grafos

Jogo educativo para ensinar **grafos**, **BFS** (busca em largura) e **DFS**
(busca em profundidade) para alunos de graduação. O aluno assume o papel de
detetive: em cada caso, precisa ligar a cena do crime ao suspeito pelo
**caminho mais curto** e, depois, comparar como a **Fila (BFS)** e a
**Pilha (DFS)** exploram o mesmo mapa.

Feito apenas com **HTML, CSS e JavaScript** (arquivo único) + **Firebase
Firestore** para o placar da turma em tempo real. Sem nenhum login por parte
dos alunos.

🔗 **Jogar agora:** <https://jogografos.web.app>

---

## 🎯 O que o jogo ensina

- **Grafos:** nós, arestas e vizinhança em um mapa investigativo.
- **Caminho mais curto:** o aluno traça a rota e o jogo revela a rota ótima.
- **BFS (Fila / FIFO):** explora em camadas e garante o menor número de passos.
- **DFS (Pilha / LIFO):** mergulha por um caminho até o fim antes de recuar.
- **Comparação prática:** animação lado a lado de como cada método percorre o
  grafo, com um quiz-relâmpago por caso.

São **5 casos** com dificuldade crescente, pontuação por rota ótima, tempo e
sequência de acertos.

---

## 🏆 Placar da turma em tempo real

- Na tela inicial, o aluno digita o **nome** e um **código de sala de 4 dígitos**
  que o professor passa na aula.
- Todos com o mesmo código veem o mesmo placar, atualizado ao vivo.
- Códigos diferentes = turmas/datas diferentes, sem misturar os placares.
- Botão **🗑️ Resetar placar da sala** zera o placar daquela sala (com confirmação).
- Sem código, o aluno joga normalmente, só não entra no placar compartilhado.

---

## 🚀 Como colocar no ar

O passo a passo completo (instalar ferramentas, login, criar o projeto,
Firestore, gerar as chaves e publicar) está no **[DEPLOY.md](DEPLOY.md)**.

Resumo para reimplantar depois de uma alteração:

```bash
cp detetive_grafos.html public/index.html
firebase deploy --only hosting
```

---

## 🗂️ Arquivos

| Arquivo | Função |
|---|---|
| `detetive_grafos.html` | Jogo completo (telas, lógica, BFS/DFS, quiz, integração Firebase). **Fonte de verdade.** |
| `public/index.html` | Cópia publicada pelo Firebase Hosting. |
| `firebase.json` | Configuração do Hosting + Firestore. |
| `.firebaserc` | Projeto Firebase padrão (`jogografos`). |
| `firestore.rules` | Regras de segurança do placar. |
| `firestore.indexes.json` | Índices do Firestore (vazio). |
| `DEPLOY.md` | Passo a passo de publicação e manutenção. |

---

## 🔒 Segurança

- As chaves em `firebaseConfig` (dentro do HTML) são **públicas por natureza** —
  vão para o navegador e podem ficar no GitHub.
- A proteção real vem das **`firestore.rules`**: leitura/escrita liberadas
  **apenas** em `salas/{codigo}/leaderboard/{alunoId}`, exigindo código de 4
  dígitos e validando tipo e tamanho de cada campo. Nenhum campo extra é aceito
  e todo o resto do banco fica bloqueado.

---

## 💡 Sem Firebase configurado?

Se o Firebase não estiver configurado ou indisponível, o jogo continua
funcionando **100%** — cada aluno joga normalmente, só o placar compartilhado
fica indisponível (com aviso na própria tela).

---

## 👤 Autor

**Prof. William Corrêa Mendes**

- Currículo Lattes: <https://lattes.cnpq.br/7726054867638395>
- Módulos de Estruturas de Dados:
  [Módulo I](https://github.com/wcmendes/ed_modulo1) ·
  [Módulo II](https://github.com/wcmendes/ed_modulo2) ·
  [Módulo III](https://github.com/wcmendes/ed_modulo3)

---

## 📄 Licença de uso

Material educacional para fins acadêmicos. Reprodução, adaptação e distribuição
permitidas, desde que seja dado o devido crédito ao autor.
