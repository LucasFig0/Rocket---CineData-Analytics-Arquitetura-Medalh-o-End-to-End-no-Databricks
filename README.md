# Contexto Organizacional e Estilo de Engenharia
Perspetiva de Equipe (Uso da Primeira Pessoa do Plural):A redação da documentação e os artefactos técnicos foram desenvolvidos adotando a primeira pessoa do plural ("nós", "decidimos", "construímos") para refletir o alinhamento com uma equipa multidisciplinar de Engenharia e Governação de Dados em contexto empresarial.Interpretação de Requisitos de Negócio:Todas as especificações técnicas, limpezas estruturais e regras analíticas foram interpretadas e endereçadas como requisitos funcionais e não-funcionais corporativos do ecossistema fictício CineData.Comentários Baseados em Decisão de Arquitetura (Decision-Driven Comments):Em todos os notebooks (Landing_to_Bronze, Bronze_to_Silver e Silver_to_Gold), cada bloco de código inclui cabeçalhos estruturados com a secção explicita "DECISÃO". Estes comentários registam a justificação técnica de desenho, impactos de governança, mitigações de erros operacionais (ANSI SQL compliance) e compromissos assumidos.   

```mermaid
flowchart TD
    LZ["<b>Landing Zone</b><br/>(Unity Catalog Volume)<br/><i>CSVs Brutos & API BACEN PTAX (via Databricks Widgets)</i>"]
    
    LZ --> toBronze["to_Bronze"]
    
    subgraph Bronze["Delta Lake (workspace.bronze)"]
        direction TB
        B1["• Append Mode<br/>• Imutabilidade<br/>• Ingestion Timestamp"]
    end
    toBronze --> Bronze

    Bronze --> toSilver["to_Silver"]

    subgraph Silver["Delta Lake (workspace.silver)"]
        direction TB
        S1["• Deduplicação (Window Functions)<br/>• Normalização & Data Quality<br/>• Proteção contra Column Shift & ANSI SQL (try_cast / try_to_date)<br/>• Forward Fill Cambial e Tipagem Estrita"]
    end
    toSilver --> Silver

    Silver --> toGold["to_Gold"]

    subgraph Gold["Delta Lake (workspace.gold)"]
        direction TB
        G1["• Modelação Dimensional Star Schema (Surrogate Keys determinísticas)<br/>• Tabelas-Ponte (Bridge Tables) para Relações N:N<br/>• fact_movies_performance (apenas filmes 'Lançado')<br/>• gold_genai_movies_context (Base de conhecimento para RAG/Vector Search)"]
    end
    toGold --> Gold

    classDef proc fill:#f9f9f9,stroke:#333,stroke-width:1px,stroke-dasharray: 4 4;
    class toBronze,toSilver,toGold proc;
```
