# Projeto91- Teste AB com Parada Otimizada

<p align="justify"><b>1. Visão Geral do Projeto</b></p>

<p align="justify">Na experimentação digital moderna, dois grandes desafios costumam coexistir:</p>
<ul>
  <li><p align="justify"><b>O problema do peeking (olhar antes da hora):</b> Analisar testes A/B continuamente inflaciona severamente a taxa de falsos positivos (Erro Tipo I).</p></li>
  <li><p align="justify"><b>A violação de normalidade em métricas de negócio:</b> Métricas cruciais como receita por usuário (ARPU) ou tempo de sessão seguem distribuições altamente assimétricas (caudas longas), quebrando as premissas dos testes paramétricos tradicionais.</p></li>
</ul>
<p align="justify">Este projeto apresenta uma solução em Python que une a teoria de Testes Sequenciais de Grupo (Group Sequential Testing) com a Reamostragem via Bootstrap, permitindo avaliações intermediárias seguras (olhares) em métricas não-normais sem perder o rigor estatístico.</p>

<hr>

<p align="justify"><b>2. Quando Usar Cada Abordagem de Fronteira?</b></p>

<p align="justify">Antes de aplicar a lógica, é fundamental entender a escolha estratégica entre as duas principais fronteiras de parada:</p>
<ul>
  <li><p align="justify"><b>O’Brien-Fleming (OBF):</b><br>
  <i>Quando usar:</i> Na grande maioria dos testes de produto e experimentação digital padrão.<br>
  <i>Por que:</i> É conservador no início (exige um Z-score muito alto para parar logo nos primeiros dias) e afrouxa os limites gradualmente. Isso preserva quase todo o poder estatístico (evita falsos negativos em efeitos pequenos), mas ainda permite encerrar o teste precocemente se houver um resultado extraordinário (home run).</p></li>
  <li><p align="justify"><b>Pocock:</b><br>
  <i>Quando usar:</i> Em testes com alto custo operacional ou financeiro (ex: campanhas de anúncios caras, ensaios clínicos, custos diários elevados de infraestrutura) ou em fases estritamente exploratórias.<br>
  <i>Por que:</i> Utiliza limiares constantes ao longo de todas as análises. É mais rápido para interromper experimentos perdedores ou inconclusivos, mas exige o preço de reduzir o poder estatístico caso o efeito real seja marginal.</p></li>
</ul>

<hr>

<p align="justify"><b>3. Funciona para Proporções?</b></p>

<p align="justify">Sim, a lógica funciona perfeitamente para proporções (como taxa de conversão, taxa de clique/CTR, taxa de rejeição), embora exista uma pequena nuance conceitual e computacional importante:</p>
<ul>
  <li><p align="justify"><b>Como o Bootstrap lida com proporções:</b> Em dados binários (0 ou 1, como convertido/não convertido), a média representa exatamente a proporção. O Bootstrap continuará estimando a diferença de médias entre tratamento e controle e calculando o erro padrão empírico de forma válida.</p></li>
  <li><p align="justify"><b>Alternativa paramétrica clássica:</b> Para proporções puras, normalmente utiliza-se o teste Z para proporções pooled baseado na distribuição binomial. No entanto, usar o Bootstrap com a mesma estrutura garante a correção para o problema do peeking em testes sequenciais e lida bem com tamanhos de amostra desbalanceados entre os grupos.</p></li>
  <li><p align="justify"><b>Exemplo prático para proporções:</b> Para testar conversões, basta passar um vetor contendo apenas zeros e uns nos conjuntos de controle e tratamento.</p></li>
</ul>

<hr>

<p align="justify"><b>4. Exemplo de Execução e Resultados</b></p>

<p align="justify">Ao rodar a simulação com distribuições exponenciais ou binárias com efeito real, o algoritmo monitora o acúmulo dos dados em K = 5 olhares planejados:</p>

<blockquote>
<p align="justify"><b>Log de Saída Esperado:</b><br>
--- INICIANDO SIMULAÇÃO SEQUENCIAL (OBF) COM BOOTSTRAP ---<br>
Olhar 1/5 (n_c=1000, n_t=1000): Z_boot = 1.85 | Limite Exigido = 2.79<br>
Olhar 2/5 (n_c=2000, n_t=2000): Z_boot = 2.41 | Limite Exigido = 1.97<br>
-> Efeito significativo detectado! Interrompendo o teste no olhar 2.</p>
</blockquote>

<p align="justify"><b>Principais Vantagens Destacadas:</b></p>
<ul>
  <li><p align="justify"><b>Robustez Contra Outliers:</b> O uso de Bootstrap assegura estimativas confiáveis do erro padrão mesmo em métricas com assimetria severa ou proporções binárias.</p></li>
  <li><p align="justify"><b>Eficiência Temporal:</b> Permite parar o experimento de forma estatisticamente rigorosa antes de atingir o tamanho total da amostra, economizando tempo e recursos de engenharia e negócio.</p></li>
</ul>
