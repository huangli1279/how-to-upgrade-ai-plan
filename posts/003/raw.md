暂时无法在飞书文档外展示此内容
1.  项目目标
1.1 数据分析的本质
暂时无法在飞书文档外展示此内容
1.2 行业产品如何解决这些问题
行业/微众
公司产品
聚集分析问题
应用效果
Palantir
Foundry

聚焦“发生了什么”->“为什么发生”->“如何行动”：通过构建客户、活动、定价、支付终端等实体与关系模型，将数据分析结果直接嵌入业务流程，支持业务决策与执行。
支付公司案例：通过优化收款、重新定价对价格不敏感的商户，并保留有流失风险的高价值客户来增加来自中小型商户的收入。
蚂蚁

DeepInsight
聚焦“发生了什么”->“为什么发生”：提供多维指标分析和智能洞察能力，并通过可调试的prompt与知识体系支持智能分析Agent持续迭代。
在风控业务中，提供小时级风险监控、多维策略分析，贴身追踪业务发展的健康度、快速调整风控策略，降低业务风险。

彭博
BQuant Desktop

聚焦“发生了什么”->“将要发生什么”：为专业用户提供端侧的数据分析环境，支持访问Bloomberg数据并构建金融分析、量化策略和数据模型。
在安全的沙盒环境中，通过广泛的高质量数据访问，构建和测试金融分析、投资策略和市场仪表板。

微众
weyan
聚焦“发生了什么”：了解经营数据有什么变化以及哪些产品带来了变化。

1.3 Data agent目标与定位
  当前阶段Data agent主要聚集在分析领域，优先解决“发生了什么”和“为什么发生”这两类问题。
  结合历史分析经验、统一的指标语义层（指标库、维度库、事件库）和AI模型的通识，理解业务分析目标、提供分析思路、获取数据并形成分析结论，帮助业务更快拿到“能用来做决策的分析结论”，从而支撑应用领域的业务行动。
  
2. 实现方案
2.1 行业 Data Agent 实现方案（黄立）
2.1 简单介绍一下palantir模式和指标语义层两种模式
2.2 以贷款产品余额分析场景为例介绍两种模式data agent分析方法的差异
构建贷款业务本体：

1.1 核心实体定义
# 本体实体结构（伪代码） 
 
class LoanProduct(Entity): 
    """贷款产品实体""" 
    product_id: str 
    product_name: str 
    interest_rate: float 
    term_months: int 
    max_amount: Decimal 
    risk_level: Enum['LOW', 'MEDIUM', 'HIGH'] 
     
class Customer(Entity): 
    """客户实体""" 
    customer_id: str 
    credit_score: int 
    income_level: Enum['LOW', 'MEDIUM', 'HIGH'] 
    region: str 
    customer_type: Enum['NEW', 'EXISTING'] 
     
class LoanAccount(Entity): 
    """贷款账户实体""" 
    account_id: str 
    product: Link[LoanProduct] 
    customer: Link[Customer] 
    balance: Decimal 
    status: Enum['ACTIVE', 'CLOSED', 'OVERDUE'] 
    disbursement_date: DateTime 
    maturity_date: DateTime 
     
class Transaction(Event): 
    """交易事件""" 
    transaction_id: str 
    account: Link[LoanAccount] 
    type: Enum['DISBURSEMENT', 'REPAYMENT', 'PREPAYMENT', 'WRITEOFF'] 
    amount: Decimal 
    timestamp: DateTime 
     
class Policy(Entity): 
    """政策实体""" 
    policy_id: str 
    policy_type: Enum['REGULATORY', 'INTERNAL', 'MARKET'] 
    effective_date: DateTime 
    description: str 
    impact_scope: List[str] 
1.2 关系定义
客户 --[申请]--> 贷款账户
贷款账户 --[属于]--> 产品
交易事件 --[影响]--> 账户余额
政策变化 --[作用于]--> 产品规则
客户 --[属于]--> 客户分群
产品 --[关联]--> 渠道
二、三层知识图谱架构
借鉴文章中的制造业三层KG架构，设计银行场景的分层结构：
2.1 交易层知识图谱（Transaction Layer KG）
数据源：
核心系统交易流水
账户余额快照
实时还款数据
节点类型：
[账户] --放款--> [交易:15亿]
[账户] --还款--> [交易:-8亿]
[账户] --提前还款--> [交易:-3亿]
[账户] --核销--> [交易:-1亿]

查询示例：
// Neo4j查询：找出Q2所有提前还款交易
MATCH (a:Account)-[r:PREPAYMENT]->(t:Transaction)
WHERE t.timestamp >= '2025-04-01' AND t.timestamp < '2025-07-01'
RETURN a.account_id, sum(t.amount) as total_prepayment
ORDER BY total_prepayment DESC
2.2 业务层知识图谱（Business Layer KG）
数据源：
产品配置表
客户分群规则
风控策略
渠道配置
节点类型：
[产品:消费贷] --定价规则--> [利率:6.5%]
[产品:消费贷] --目标客群--> [客户分群:高信用]
[客户分群:高信用] --风控策略--> [审批通过率:85%]
[渠道:线上] --获客成本--> [成本:300元/户]

关键关系：
产品 --适用于--> 客户分群
客户分群 --匹配--> 风控策略
渠道 --推广--> 产品
政策 --约束--> 产品规则

2.3 战略层知识图谱（Strategic Layer KG）
数据源：
监管政策文档
市场研究报告
竞品分析
宏观经济指标
节点类型：
[央行政策:LPR下调] --影响--> [市场利率]
[市场利率] --驱动--> [提前还款率]
[监管要求:额度限制] --约束--> [产品设计]
[竞品:银行B] --价格战--> [市场份额]

LLM自动提取示例：
# 使用LLM提取监管文件中的规则 
policy_text = """ 
根据银保监会最新规定，个人消费贷款单户授信额度不得超过20万元， 
且贷款用途应严格限定于消费领域，禁止流入房地产市场。 
""" 
 
# LLM提取结构化规则 
extracted_rules = llm.extract_rules(policy_text) 
# 输出： 
# { 
#   "rule_type": "REGULATORY", 
#   "constraint": "max_amount <= 200000", 
#   "scope": "个人消费贷", 
#   "prohibition": "禁止流入房地产" 
# } 
 
# 自动注入知识图谱 
kg.add_constraint( 
    entity="LoanProduct", 
    constraint="max_amount <= 200000", 
    source="银保监会2025-03-01" 
) 
三、归因分析引擎设计
3.1 余额变化公式拆解
Δ余额(t) = 新增放款(t) - 正常还款(t) - 提前还款(t) - 核销(t) + 调整(t)

一级归因：
def level1_attribution(start_date, end_date): 
    """一级归因：余额变化拆解""" 
     
    # 从知识图谱查询 
    query = """ 
    MATCH (a:Account)-[t:Transaction]->(tx) 
    WHERE tx.timestamp >= $start AND tx.timestamp < $end 
    RETURN  
        tx.type as transaction_type, 
        sum(tx.amount) as total_amount 
    """ 
     
    results = { 
        'disbursement': +15_000_000_000,  # 新增放款 
        'repayment': -8_000_000_000,      # 正常还款 
        'prepayment': -3_000_000_000,     # 提前还款 
        'writeoff': -1_000_000_000        # 核销 
    } 
     
    return results 

二级归因：按维度穿透
def level2_attribution(transaction_type, dimensions): 
    """二级归因：多维度拆解""" 
     
    # 示例：提前还款按客户类型拆解 
    query = """ 
    MATCH (c:Customer)-[:OWNS]->(a:Account)-[t:PREPAYMENT]->(tx) 
    WHERE tx.timestamp >= $start AND tx.timestamp < $end 
    RETURN  
        c.customer_type, 
        c.credit_score_band, 
        sum(tx.amount) as prepayment_amount 
    """ 
     
    # 结果示例 
    results = { 
        ('NEW', 'HIGH'): -500_000_000, 
        ('NEW', 'MEDIUM'): -200_000_000, 
        ('EXISTING', 'HIGH'): -1_800_000_000,  # 最大贡献者 
        ('EXISTING', 'MEDIUM'): -500_000_000 
    } 
     
    return results 
3.2 因果推断模型
借鉴 Palantir 的证据链（Evidence Chain）概念：
class CausalChain: 
    """因果链分析""" 
     
    def trace_root_cause(self, effect: str, kg: KnowledgeGraph): 
        """追溯根因""" 
         
        # 示例：提前还款率上升的根因追溯 
        chain = kg.query(""" 
            MATCH path = (root)-[*]->(effect:Metric {name: '提前还款率'}) 
            WHERE effect.value > effect.threshold 
            RETURN path 
            ORDER BY length(path) DESC 
            LIMIT 1 
        """) 
         
        # 返回因果路径 
        return [ 
            "央行降息25BP", 
            "→ 市场利率下行", 
            "→ 客户再融资成本降低", 
            "→ 提前还款意愿增强", 
            "→ 提前还款率上升2.3%", 
            "→ 存量余额加速衰减" 
        ] 
3.3 Shapley值归因
from itertools import combinations 
 
def shapley_attribution(factors, value_function): 
    """计算每个因素的Shapley值""" 
     
    n = len(factors) 
    shapley_values = {} 
     
    for factor in factors: 
        marginal_contributions = [] 
         
        # 遍历所有可能的联盟 
        for r in range(n): 
            for coalition in combinations([f for f in factors if f != factor], r): 
                coalition_set = set(coalition) 
                 
                # 计算边际贡献 
                v_with = value_function(coalition_set | {factor}) 
                v_without = value_function(coalition_set) 
                marginal = v_with - v_without 
                 
                # 加权 
                weight = (factorial(r) * factorial(n - r - 1)) / factorial(n) 
                marginal_contributions.append(weight * marginal) 
         
        shapley_values[factor] = sum(marginal_contributions) 
     
    return shapley_values 
 
# 应用示例 
factors = ['提前还款率', '新增放款', '核销率', '正常还款'] 
shapley_result = shapley_attribution(factors, balance_change_function) 
 
# 输出： 
# { 
#   '提前还款率': -40%,  # 最大负向贡献 
#   '新增放款': -35%, 
#   '核销率': -15%, 
#   '正常还款': -10% 
# } 
四、LLM增强的智能分析（LSDTs应用）
4.1 自动知识提取
class PolicyKnowledgeExtractor: 
    """政策知识自动提取器""" 
     
    def __init__(self, llm_client): 
        self.llm = llm_client 
         
    def extract_from_document(self, doc_path: str): 
        """从监管文件提取规则""" 
         
        # 读取文档 
        content = read_pdf(doc_path) 
         
        # LLM提取 
        prompt = f""" 
        从以下监管文件中提取影响贷款业务的关键规则： 
         
        {content} 
         
        请以JSON格式返回： 
        {{ 
            "rules": [ 
                {{ 
                    "type": "额度限制/利率约束/用途限制", 
                    "scope": "适用产品", 
                    "constraint": "具体约束", 
                    "effective_date": "生效日期" 
                }} 
            ] 
        }} 
        """ 
         
        rules = self.llm.generate(prompt) 
         
        # 注入知识图谱 
        for rule in rules['rules']: 
            self.inject_to_kg(rule) 
         
        return rules 
     
    def inject_to_kg(self, rule): 
        """将规则注入知识图谱""" 
        kg.execute(""" 
            CREATE (p:Policy { 
                type: $type, 
                scope: $scope, 
                constraint: $constraint, 
                effective_date: $date 
            }) 
             
            MATCH (prod:Product {name: $scope}) 
            CREATE (p)-[:CONSTRAINS]->(prod) 
        """, rule) 
4.2 智能问答系统
class ConversationalBI: 
    """对话式BI系统""" 
     
    def answer_question(self, question: str): 
        """回答业务问题""" 
         
        # 示例问题："为什么Q2消费贷余额下降了？" 
         
        # 步骤1：意图识别 
        intent = self.llm.classify_intent(question) 
        # 输出：{"intent": "attribution_analysis", "entity": "消费贷", "period": "Q2"} 
         
        # 步骤2：知识图谱查询 
        kg_query = self.generate_cypher(intent) 
        data = kg.execute(kg_query) 
         
        # 步骤3：归因计算 
        attribution = self.calculate_attribution(data) 
         
        # 步骤4：生成自然语言解释 
        explanation = self.llm.generate_explanation( 
            question=question, 
            data=attribution, 
            context=self.get_context_from_kg() 
        ) 
         
        return { 
            "answer": explanation, 
            "visualization": self.generate_chart(attribution), 
            "evidence_chain": self.trace_causal_path(attribution) 
        } 
 
# 输出示例 
""" 
Q2消费贷余额下降5亿元，主要原因包括： 
 
1. **提前还款激增**（贡献度40%，-2亿元） 
   - 根因：央行3月降息25BP，市场利率下行 
   - 影响客群：存量高信用客户（信用分>750） 
   - 证据链：央行政策 → 市场利率 → 再融资成本 → 提前还款率 
    
2. **新增放款减少**（贡献度35%，-1.75亿元） 
   - 根因：风控政策收紧，审批通过率从65%降至58% 
   - 影响产品：线上消费贷 
   - 证据链：监管要求 → 内部政策 → 审批标准 → 放款量 
    
3. **核销增加**（贡献度25%，-1.25亿元） 
   - 根因：2024年逾期账户集中处置 
   - 影响客群：低信用客户（信用分<600） 
 
[查看详细数据] [导出报告] [设置预警] 
""" 
4.3 自适应预警系统
class AdaptiveAlertSystem: 
    """自适应预警系统""" 
     
    def monitor_balance_change(self): 
        """监控余额异常变化""" 
         
        # 实时计算 
        current_balance = self.get_current_balance() 
        expected_balance = self.predict_balance() 
        deviation = abs(current_balance - expected_balance) / expected_balance 
         
        if deviation > 0.05:  # 偏离超过5% 
            # 自动归因分析 
            root_causes = self.trace_root_causes() 
             
            # LLM生成预警报告 
            alert = self.llm.generate_alert( 
                deviation=deviation, 
                root_causes=root_causes, 
                historical_context=self.get_similar_cases() 
            ) 
             
            # 推送通知 
            self.send_alert(alert) 
             
            return alert 
 
# 预警示例 
""" 
⚠️ 余额异常预警 
 
【异常情况】 
消费贷余额较预期低8%（实际103亿 vs 预期112亿） 
 
【根因分析】 
1. 提前还款率异常上升（历史均值1.5%，当前3.8%） 
   - 置信度：92% 
   - 相关因素：市场利率下行（相关系数0.89） 
 
2. 新增放款不及预期（计划18亿，实际15亿） 
   - 置信度：85% 
   - 相关因素：审批政策调整 
 
【建议措施】 
• 紧急：评估利率定价策略，考虑提前还款违约金 
• 重要：复核风控政策，平衡风险与增长 
• 关注：监控竞品动态，防止客户流失 
 
【历史相似案例】 
2024年Q3曾出现类似情况，当时采取的措施是... 
""" 
五、可视化呈现（基于Palantir Foundry风格）
5.1 本体驱动的交互式看板
// 前端伪代码：本体驱动的动态看板  class OntologyDrivenDashboard {     constructor(ontology, kg) {         this.ontology = ontology;  // 本体定义         this.kg = kg;              // 知识图谱连接     }          renderBalanceAttribution() {         // 从本体获取实体定义         const entities = this.ontology.getEntities(['LoanAccount', 'Transaction']);                  // 从知识图谱查询数据         const data = this.kg.query(`             MATCH (a:LoanAccount)-[t:Transaction]->()             WHERE t.timestamp >= $start AND t.timestamp < $end             RETURN t.type, sum(t.amount) as total         `);                  // 渲染瀑布图         this.renderWaterfallChart(data);                  // 绑定下钻事件         this.bindDrillDown((transactionType) => {             // 点击后自动查询下一层级             const detailData = this.kg.query(`                 MATCH (c:Customer)-[:OWNS]->(a:LoanAccount)-[t:${transactionType}]->()                 RETURN c.customer_type, sum(t.amount)             `);             this.renderHeatmap(detailData);         });     } } 
5.2 证据链可视化
┌─────────────────────────────────────────────────────────┐
│ 提前还款率上升的证据链                                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  [央行政策]                                              │
│      ↓ 置信度: 95%                                       │
│  降息25BP (2025-03-15)                                   │
│      ↓                                                   │
│  [市场利率]                                              │
│      ↓ 相关系数: 0.92                                    │
│  LPR下调至3.65%                                          │
│      ↓                                                   │
│  [客户行为]                                              │
│      ↓ 因果强度: 0.87                                    │
│  再融资成本降低                                           │
│      ↓                                                   │
│  [业务指标]                                              │
│      ↓ 影响程度: +2.3%                                   │
│  提前还款率上升                                           │
│      ↓                                                   │
│  [财务结果]                                              │
│  存量余额减少3亿元                                        │
│                                                         │
│  [数据来源]                                              │
│  • 央行官网公告                                          │
│  • 核心系统交易数据                                       │
│  • 客户行为分析模型                                       │
│                                                         │
└─────────────────────────────────────────────────────────┘

BI+指标语义：
传统BI语义层方案：贷款余额分析实施方案
一、架构设计
1.1 整体架构
┌─────────────────────────────────────────────────────────┐
│                    展现层                                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │ Tableau  │  │ Power BI │  │  自研前端 │              │
│  └──────────┘  └──────────┘  └──────────┘              │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│                  语义层（Semantic Layer）                │
│  ┌────────────────────────────────────────────────────┐ │
│  │  指标定义层                                         │ │
│  │  • 余额类指标（期初/期末/平均余额）                  │ │
│  │  • 变动类指标（新增/还款/核销）                      │ │
│  │  • 比率类指标（增长率/还款率/逾期率）                │ │
│  └────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────┐ │
│  │  维度定义层                                         │ │
│  │  • 时间维度（日/周/月/季/年）                        │ │
│  │  • 产品维度（产品线/产品/子产品）                    │ │
│  │  • 客户维度（客户类型/信用等级/地区）                │ │
│  │  • 渠道维度（线上/线下/代理）                        │ │
│  └────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────┐ │
│  │  业务逻辑层                                         │ │
│  │  • 计算规则（余额=期初+新增-还款-核销）              │ │
│  │  • 归因公式（Δ余额拆解逻辑）                        │ │
│  │  • 数据权限（行级安全）                             │ │
│  └────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│                  数据仓库层（DWH）                       │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │  DWS层   │  │  DWD层   │  │  ODS层   │              │
│  │ (汇总表) │  │ (明细表) │  │ (原始表) │              │
│  └──────────┘  └──────────┘  └──────────┘              │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│                    数据源层                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │ 核心系统 │  │ 信贷系统 │  │ 风控系统 │              │
│  └──────────┘  └──────────┘  └──────────┘              │
└─────────────────────────────────────────────────────────┘
二、数据建模（Kimball星型模型）
2.1 事实表设计
事实表1：贷款账户余额快照表（fact_loan_balance_snapshot）
CREATE TABLE fact_loan_balance_snapshot ( 
    snapshot_date DATE NOT NULL,              -- 快照日期 
    account_id VARCHAR(32) NOT NULL,          -- 账户ID 
    product_key INT,                          -- 产品维度键 
    customer_key INT,                         -- 客户维度键 
    channel_key INT,                          -- 渠道维度键 
     
    -- 度量字段 
    balance_amount DECIMAL(18,2),             -- 余额 
    principal_amount DECIMAL(18,2),           -- 本金 
    interest_amount DECIMAL(18,2),            -- 利息 
    overdue_amount DECIMAL(18,2),             -- 逾期金额 
    overdue_days INT,                         -- 逾期天数 
     
    PRIMARY KEY (snapshot_date, account_id) 
); 
 
-- 分区策略 
ALTER TABLE fact_loan_balance_snapshot  
PARTITION BY RANGE (snapshot_date); 

事实表2：贷款交易明细表（fact_loan_transaction）
CREATE TABLE fact_loan_transaction ( 
    transaction_id VARCHAR(32) PRIMARY KEY, 
    transaction_date DATE NOT NULL, 
    account_id VARCHAR(32) NOT NULL, 
    product_key INT, 
    customer_key INT, 
    channel_key INT, 
     
    -- 交易类型 
    transaction_type VARCHAR(20),  -- DISBURSEMENT/REPAYMENT/PREPAYMENT/WRITEOFF 
     
    -- 度量字段 
    transaction_amount DECIMAL(18,2), 
    principal_amount DECIMAL(18,2), 
    interest_amount DECIMAL(18,2), 
    fee_amount DECIMAL(18,2), 
     
    -- 审计字段 
    created_at TIMESTAMP, 
    updated_at TIMESTAMP 
); 
 
-- 索引 
CREATE INDEX idx_trans_date ON fact_loan_transaction(transaction_date); 
CREATE INDEX idx_trans_account ON fact_loan_transaction(account_id); 
CREATE INDEX idx_trans_type ON fact_loan_transaction(transaction_type); 
2.2 维度表设计
维度表1：产品维度表（dim_product）
CREATE TABLE dim_product ( 
    product_key INT PRIMARY KEY,              -- 代理键 
    product_id VARCHAR(32) UNIQUE,            -- 业务键 
    product_name VARCHAR(100), 
    product_line VARCHAR(50),                 -- 产品线 
    product_category VARCHAR(50),             -- 产品类别 
     
    -- 产品属性 
    interest_rate DECIMAL(5,4), 
    term_months INT, 
    max_amount DECIMAL(18,2), 
    min_amount DECIMAL(18,2), 
     
    -- SCD Type 2字段 
    effective_date DATE, 
    expiry_date DATE, 
    is_current BOOLEAN, 
     
    -- 层级字段（用于下钻） 
    level1_name VARCHAR(50),  -- 产品线 
    level2_name VARCHAR(50),  -- 产品类别 
    level3_name VARCHAR(50),  -- 具体产品 
     
    created_at TIMESTAMP, 
    updated_at TIMESTAMP 
); 

维度表2：客户维度表（dim_customer）
CREATE TABLE dim_customer ( 
    customer_key INT PRIMARY KEY, 
    customer_id VARCHAR(32) UNIQUE, 
    customer_name VARCHAR(100), 
     
    -- 客户分类 
    customer_type VARCHAR(20),      -- NEW/EXISTING 
    customer_segment VARCHAR(20),   -- RETAIL/SME/CORPORATE 
     
    -- 信用信息 
    credit_score INT, 
    credit_grade VARCHAR(10),       -- AAA/AA/A/BBB/BB/B/C 
     
    -- 人口统计 
    age_group VARCHAR(20), 
    gender VARCHAR(10), 
    education VARCHAR(50), 
    occupation VARCHAR(50), 
     
    -- 地理信息 
    province VARCHAR(50), 
    city VARCHAR(50), 
    district VARCHAR(50), 
     
    -- 收入信息 
    income_level VARCHAR(20),       -- HIGH/MEDIUM/LOW 
    monthly_income DECIMAL(18,2), 
     
    -- SCD Type 2 
    effective_date DATE, 
    expiry_date DATE, 
    is_current BOOLEAN 
); 

维度表3：时间维度表（dim_date）
CREATE TABLE dim_date ( 
    date_key INT PRIMARY KEY, 
    full_date DATE UNIQUE, 
     
    -- 日期层级 
    year INT, 
    quarter INT, 
    month INT, 
    week INT, 
    day INT, 
     
    -- 业务日期 
    fiscal_year INT, 
    fiscal_quarter INT, 
    fiscal_month INT, 
     
    -- 标识字段 
    is_weekend BOOLEAN, 
    is_holiday BOOLEAN, 
    holiday_name VARCHAR(50), 
     
    -- 相对日期 
    is_current_day BOOLEAN, 
    is_current_month BOOLEAN, 
    is_current_quarter BOOLEAN, 
     
    -- 显示字段 
    year_month VARCHAR(7),      -- 2025-03 
    quarter_name VARCHAR(10),   -- 2025Q1 
    month_name VARCHAR(20),     -- 2025年3月 
    weekday_name VARCHAR(10)    -- 星期一 
); 

维度表4：渠道维度表（dim_channel）
CREATE TABLE dim_channel ( 
    channel_key INT PRIMARY KEY, 
    channel_id VARCHAR(32) UNIQUE, 
    channel_name VARCHAR(100), 
     
    -- 渠道层级 
    channel_type VARCHAR(20),       -- ONLINE/OFFLINE/AGENT 
    channel_category VARCHAR(50),   -- APP/WEB/BRANCH/PARTNER 
     
    -- 渠道属性 
    acquisition_cost DECIMAL(10,2), 
    conversion_rate DECIMAL(5,4), 
     
    effective_date DATE, 
    expiry_date DATE, 
    is_current BOOLEAN 
); 
三、语义层定义（以dbt为例）
3.1 指标定义（Metrics）
# models/metrics/loan_balance_metrics.yml 
 
version: 2 
 
metrics: 
  # 基础指标 
  - name: total_balance 
    label: 贷款总余额 
    model: ref('fact_loan_balance_snapshot') 
    description: 所有贷款账户的余额总和 
    calculation_method: sum 
    expression: balance_amount 
    timestamp: snapshot_date 
    time_grains: [day, week, month, quarter, year] 
    dimensions: 
      - product_key 
      - customer_key 
      - channel_key 
     
  - name: avg_balance 
    label: 平均余额 
    model: ref('fact_loan_balance_snapshot') 
    calculation_method: average 
    expression: balance_amount 
    timestamp: snapshot_date 
    time_grains: [day, week, month, quarter, year] 
     
  # 变动类指标 
  - name: new_disbursement 
    label: 新增放款 
    model: ref('fact_loan_transaction') 
    calculation_method: sum 
    expression: transaction_amount 
    timestamp: transaction_date 
    filters: 
      - field: transaction_type 
        operator: '=' 
        value: "'DISBURSEMENT'" 
    time_grains: [day, week, month, quarter, year] 
     
  - name: total_repayment 
    label: 还款总额 
    model: ref('fact_loan_transaction') 
    calculation_method: sum 
    expression: transaction_amount 
    filters: 
      - field: transaction_type 
        operator: 'IN' 
        value: "('REPAYMENT', 'PREPAYMENT')" 
     
  - name: prepayment_amount 
    label: 提前还款金额 
    model: ref('fact_loan_transaction') 
    calculation_method: sum 
    expression: transaction_amount 
    filters: 
      - field: transaction_type 
        operator: '=' 
        value: "'PREPAYMENT'" 
     
  - name: writeoff_amount 
    label: 核销金额 
    model: ref('fact_loan_transaction') 
    calculation_method: sum 
    expression: transaction_amount 
    filters: 
      - field: transaction_type 
        operator: '=' 
        value: "'WRITEOFF'" 
     
  # 比率类指标 
  - name: balance_growth_rate 
    label: 余额增长率 
    model: ref('fact_loan_balance_snapshot') 
    calculation_method: derived 
    expression: | 
      (SUM(CASE WHEN snapshot_date = CURRENT_DATE THEN balance_amount END) -  
       SUM(CASE WHEN snapshot_date = CURRENT_DATE - INTERVAL '1 month' THEN balance_amount END)) / 
      NULLIF(SUM(CASE WHEN snapshot_date = CURRENT_DATE - INTERVAL '1 month' THEN balance_amount END), 0)       
     
  - name: prepayment_rate 
    label: 提前还款率 
    calculation_method: derived 
    expression: | 
      SUM(CASE WHEN transaction_type = 'PREPAYMENT' THEN transaction_amount END) / 
      NULLIF(SUM(balance_amount), 0)       
3.2 计算逻辑定义（dbt模型）
-- models/marts/loan_balance_attribution.sql
{{
  config(
    materialized='table',
    partition_by={
      "field": "analysis_date",
      "data_type": "date",
      "granularity": "day"
    }
  )
}}
WITH balance_snapshot AS (
    SELECT
        snapshot_date,
        product_key,
        customer_key,
        channel_key,
        SUM(balance_amount) as total_balance
    FROM {{ ref('fact_loan_balance_snapshot') }}
    GROUP BY 1, 2, 3, 4
),
transactions AS (
    SELECT
        transaction_date,
        product_key,
        customer_key,
        channel_key,
        transaction_type,
        SUM(transaction_amount) as amount
    FROM {{ ref('fact_loan_transaction') }}
    GROUP BY 1, 2, 3, 4, 5
),
balance_change AS (
    SELECT
        b_current.snapshot_date as analysis_date,
        b_current.product_key,
        b_current.customer_key,
        b_current.channel_key,
        
        -- 期初期末余额
        b_previous.total_balance as beginning_balance,
        b_current.total_balance as ending_balance,
        b_current.total_balance - b_previous.total_balance as balance_change,
        
        -- 交易拆解
        COALESCE(t_disbursement.amount, 0) as new_disbursement,
        COALESCE(t_repayment.amount, 0) as regular_repayment,
        COALESCE(t_prepayment.amount, 0) as prepayment,
        COALESCE(t_writeoff.amount, 0) as writeoff,
        
        -- 归因计算
        COALESCE(t_disbursement.amount, 0) as contribution_disbursement,
        -COALESCE(t_repayment.amount, 0) as contribution_repayment,
        -COALESCE(t_prepayment.amount, 0) as contribution_prepayment,
        -COALESCE(t_writeoff.amount, 0) as contribution_writeoff
        
    FROM balance_snapshot b_current
    LEFT JOIN balance_snapshot b_previous
        ON b_current.product_key = b_previous.product_key
        AND b_current.customer_key = b_previous.customer_key
        AND b_current.channel_key = b_previous.channel_key
        AND b_previous.snapshot_date = b_current.snapshot_date - INTERVAL '1 day'
    
    LEFT JOIN (
        SELECT * FROM transactions WHERE transaction_type = 'DISBURSEMENT'
    ) t_disbursement
        ON b_current.snapshot_date = t_disbursement.transaction_date
        AND b_current.product_key = t_disbursement.product_key
        AND b_current.customer_key = t_disbursement.customer_key
        AND b_current.channel_key = t_disbursement.channel_key
    
    LEFT JOIN (
        SELECT * FROM transactions WHERE transaction_type = 'REPAYMENT'
    ) t_repayment
        ON b_current.snapshot_date = t_repayment.transaction_date
        AND b_current.product_key = t_repayment.product_key
        AND b_current.customer_key = t_repayment.customer_key
        AND b_current.channel_key = t_repayment.channel_key
    
    LEFT JOIN (
        SELECT * FROM transactions WHERE transaction_type = 'PREPAYMENT'
    ) t_prepayment
        ON b_current.snapshot_date = t_prepayment.transaction_date
        AND b_current.product_key = t_prepayment.product_key
        AND b_current.customer_key = t_prepayment.customer_key
        AND b_current.channel_key = t_prepayment.channel_key
    
    LEFT JOIN (
        SELECT * FROM transactions WHERE transaction_type = 'WRITEOFF'
    ) t_writeoff
        ON b_current.snapshot_date = t_writeoff.transaction_date
        AND b_current.product_key = t_writeoff.product_key
        AND b_current.customer_key = t_writeoff.customer_key
        AND b_current.channel_key = t_writeoff.channel_key
)
SELECT
    bc.*,
    
    -- 关联维度表
    p.product_name,
    p.product_line,
    c.customer_type,
    c.credit_grade,
    ch.channel_name,
    ch.channel_type,
    
    -- 计算贡献度百分比
    CASE 
        WHEN bc.balance_change != 0 THEN 
            bc.contribution_disbursement / NULLIF(ABS(bc.balance_change), 0)
        ELSE 0 
    END as pct_contribution_disbursement,
    
    CASE 
        WHEN bc.balance_change != 0 THEN 
            bc.contribution_repayment / NULLIF(ABS(bc.balance_change), 0)
        ELSE 0 
    END as pct_contribution_repayment,
    
    CASE 
        WHEN bc.balance_change != 0 THEN 
            bc.contribution_prepayment / NULLIF(ABS(bc.balance_change), 0)
        ELSE 0 
    END as pct_contribution_prepayment,
    
    CASE 
        WHEN bc.balance_change != 0 THEN 
            bc.contribution_writeoff / NULLIF(ABS(bc.balance_change), 0)
        ELSE 0 
    END as pct_contribution_writeoff
FROM balance_change bc
LEFT JOIN {{ ref('dim_product') }} p ON bc.product_key = p.product_key AND p.is_current
LEFT JOIN {{ ref('dim_customer') }} c ON bc.customer_key = c.customer_key AND c.is_current
LEFT JOIN {{ ref('dim_channel') }} ch ON bc.channel_key = ch.channel_key AND ch.is_current
四、BI工具实现（以Tableau为例）
4.1 数据源连接
# Tableau数据源配置 
 
datasource: 
  name: "贷款余额归因分析" 
  connection: 
    type: "postgresql" 
    server: "dwh.company.com" 
    database: "analytics" 
    schema: "marts" 
   
  tables: 
    - name: "loan_balance_attribution" 
      alias: "归因分析主表" 
    - name: "dim_product" 
      alias: "产品维度" 
      join: 
        type: "left" 
        on: "loan_balance_attribution.product_key = dim_product.product_key" 
    - name: "dim_customer" 
      alias: "客户维度" 
      join: 
        type: "left" 
        on: "loan_balance_attribution.customer_key = dim_customer.customer_key" 
4.2 计算字段定义
// Tableau计算字段 
 
// 1. 余额变化 
[Ending Balance] - [Beginning Balance] 
 
// 2. 余额增长率 
([Ending Balance] - [Beginning Balance]) / [Beginning Balance] 
 
// 3. 提前还款率 
[Prepayment] / [Beginning Balance] 
 
// 4. 归因贡献度排名 
RANK(SUM([Contribution Amount])) 
 
// 5. 同比变化 
(SUM([Balance]) - LOOKUP(SUM([Balance]), -12)) / ABS(LOOKUP(SUM([Balance]), -12)) 
 
// 6. 移动平均 
WINDOW_AVG(SUM([Balance]), -6, 0) 
 
// 7. 累计余额 
RUNNING_SUM(SUM([Balance Change])) 
4.3 仪表板设计
仪表板1：余额归因总览
┌─────────────────────────────────────────────────────────────┐
│  贷款余额归因分析 - 2025年Q2                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ 期末余额     │  │ 余额变化     │  │ 增长率       │        │
│  │ 103亿       │  │ +3亿        │  │ +3.0%       │        │
│  │ ↑ vs Q1     │  │ ↓ vs Q1     │  │ ↓ vs Q1     │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐ │
│  │  余额变化瀑布图                                        │ │
│  │                                                       │ │
│  │  期初100 ━━━━━━━━━━━━━━━━

2.2 Data Agent整体技术架构
Data Agent 通过多 Agent 协同完成需求理解、数据获取、分析与报告生成，并通过评估与反思机制持续优化分析能力，底层依托统一的数据基础、知识沉淀与分析工具体系支撑。
暂时无法在飞书文档外展示此内容
3. 当前项目进展
阶段
目标
工作内容
当前状态
风险
1. 核心流程闭环验证（PoC）

以“余额分析”主题为试点，完成端到端分析闭环建设（用户提问→ 问题澄清→ 分析框架生成→ 数据获取→ 数据分析→ 初版报告输出），并验证流程可行性。
1. “余额分析”主题知识梳理及知识结构定义
2. 主体agent的开发
3. 前后端工作流的串联
基本完成：
1. 技术开发已完成
2. 测试优化中
3. 生产环境部署准备中
进度风险：生产环境部署依赖基础设施支持
2. “余额分析”单主题可用（生产化准备）
核心能力优化与稳定性调优，确保单主题分析结果达到可控质量水平，支持小范围真实业务使用。
1. 分析框架准确性优化
2. 数据获取准确性优化、性能优化
3. 数据分析深度优化
4. 分析报告效果优化
当前重点：
1. 评测集生成中

3. 多主题扩展（平台化验证）
从单主题验证走向多主题扩展，验证平台化能力。
1. 补齐多主题知识
2. 保证平台多主题稳定性
未启动：
多主题知识沉淀同步进行

4. 全面应用（规模化落地）
在更多业务场景中稳定应用，形成可持续运行的分析能力。
1. 覆盖更多业务主题
2. 建立标准化接入流程
3. 建立持续优化与反馈机制
未启动


- 整体进展：
  当前处于第一阶段收尾与第二阶段启动交叉阶段。余额分析主题端到端流程验证跑通，生产部署准备中。
- 本周进展：
  - 完成测试环境部署
  - 完成生产指标语义的导出与更新
  - 完成余额分析主题50个评测问题的准备和测试
- 下周计划：
  - 完成生产环境部署，验证生产链路稳定性
  - 完成余额分析主题50个评测问题的测试结果标注，建立质量评测基线

4. 当前项目效果（旭辉）
整体结论：
输入50个问题进行测试，分析框架达到预期的占比为54%。
判断标准：
符合以下四项则为符合预期，不符合则为不符合预期，符合预期且某些分析思路出彩则为超出预期：
1、口径确认完整，是否将模糊信息确认清楚
2、分析思路是否符合常规分析思路
3、分析思路与分析框架中取数是否一一对应
4、分析是否超出分析范围
问题分布
类型
评价
个数
占比
余额查询类

超过预期
1
7.14%

符合预期
7
50.00%

不符合预期
6
42.86%
环比变化分析类

超出预期
0
0.00%

符合预期
14
51.85%

不符合预期
13
48.15%
影响判断类
超出预期
1
16.67%

符合预期
3
50.00%

不符合预期
2
33.33%
模糊问题
符合预期
2
50.00%

不符合预期
2
50.00%
案例演示
1、回答符合预期
25年4月底环比上月底余额有什么变化，变化的原因是什么？
对于指标体系类似问题，符合分析逻辑:确认波动情况——>客群拆解——>因子拆解——>关联指标分析&维度结构拆解，且拆解方式与分析方法一致。
第一层：总量层分析  
📈 对比 25 年 4 月底 vs 25 年 3 月底贷款余额，先看余额环比变动的绝对值与环比率，判断是增长、下降还是增速放缓  
📊 结合最近 12 个月月末余额走势，识别 4 月是否属于正常波动区间，还是出现异常拐点  
🎯 同步关注有余额客户数、人均余额的变化，初步判断是“客户规模变化”还是“单客余额变化”驱动总余额波动  
→ 先确认 4 月底余额变化是否显著，以及变化主要来自“人”还是“额”

第二层：客群结构层分析  
👥 将总余额拆为首借客户余额与存量客户余额，判断本次环比变化主要由哪类客群贡献  
📊 对比两类客群在 4 月底的余额环比增减额、增减幅及占比变化，识别结构是否发生迁移  
🧩 若总余额下滑，重点看是首借贡献减弱，还是存量流失加快；若总余额增长，重点看是首借拉动还是存量扩张  
→ 明确余额变化的核心来源客群，是首借端问题还是存量端问题

第三层：行为拆解层分析  
📈 若是首借客户贡献变化，重点拆解“首借客户余额 = 新借据发放额 × 留存率”，看是首借规模下降还是当月留存变弱  
👥 对首借发放额继续拆为“首借客户数 × 人均借款金额”，再看首借客户数背后的来访率、申请率、批核率漏斗是否走弱  
💳 若是存量客户贡献变化，重点拆解“存量余额变化 = 新发放借据余额 − 提前还款流失余额”，判断是新增借款不足，还是提前结清/还款流失增加  
→ 将余额变化落到直接业务行为：新增放款、借款人数、人均借款、留存率、提前结清

第四层：指标联动与原因定位层分析  
🎯 首借侧重点联动：开通未借人数、来访率、申请率、批核率、人均授信、额度使用率，判断是流量、转化、风控还是额度策略影响  
📊 存量侧重点联动：借款客户数、人均借款金额、新借据当月还款额、旧借据提前结清率，判断是复借需求变化还是还款流失加剧  
🌍 结合常用切片维度进一步定位原因，如渠道、利率分段、额度分段、地区、静止时间分段、营销活动或策略调整，识别是否为某些客群/策略的局部异动带动整体变化  
→ 最终把原因归类为三类：量的问题（人数）、率的问题（转化/留存/结清率）、结构的问题（客群/渠道/策略）
2、回答超出预期
a、今年年底微粒贷的余额是多少？
今年年底余额实际没有，会同步确认是否需要预测，以及按照预测的分析逻辑进行
### 一、结果口径
**问题1：本次回答按“预测今年年底余额”处理吗？**
- 默认答案：是，按预测2026年12月末微粒贷贷款余额处理
- 可选项：是（预测年底余额）、否（仅查询已发生结果）
- 说明：当前尚未到年底，如按“今年年底”回答，通常需先确认是否采用预测口径

**问题2：本次更希望输出哪类结果？**
- 默认答案：优先给出年底余额规模及大致区间
- 可选项：仅给余额值、余额值+趋势判断、余额值+原因拆解
- 说明：用户当前问题是“年底余额是多少”，默认优先回答规模结果；若需要，也可同步补充趋势或原因解释

### 二、业务背景补充
**问题3：今年剩余月份是否已知有较大营销活动需要纳入判断？**
- 默认答案：无特殊营销活动
- 可选项：免息券活动、折扣券活动、拉新活动、促活活动
- 说明：年底余额预测通常会明显受营销拉动影响，尤其会影响首借与存量借款发放强度

**问题4：今年剩余月份是否有重要策略调整需要纳入判断？**
- 默认答案：无策略调整
- 可选项：额度调整、利率调整、风控策略变化、审批策略变化、借款上限调整
- 说明：策略变化会直接影响借款人数、人均借款金额和提前结清行为，是年底余额预测的重要背景变量

**问题5：是否需要按“首借客户/存量客户”拆解年底余额贡献？**
- 默认答案：否，先看整体余额
- 可选项：是、否
- 说明：若需要解释“为什么会是这个年底余额”，通常要进一步拆解首借与存量两类客群的贡献情况
b、24年11月放开了单笔4W限制，对余额有什么影响呢？
1、会提示是否与其他策略叠加影响，之前产品侧自行分析时并未考虑到；
2、给出不同分析思路，不仅考虑了单笔放开4W限制对借多少有影响，也会考虑到借大笔后客户提前结清和借款长短的问题
### 一、分析范围
**问题1：本次“放开单笔4W限制”的影响评估，观察窗口按哪个范围看？**
- 默认答案：以2024年11月为政策生效点，对比前后各2-3个月（建议看2024年9月-2025年1月）
- 可选项：仅看2024年11月当月、前后各2个月、前后各3个月、看更长周期
- 说明：该问题的核心是策略前后对比，需先确认观察窗口，避免把短期波动或季节性误判为策略影响

**问题2：本次分析口径是看全量余额，还是重点看“命中单笔4W放开策略”的客群余额？**
- 默认答案：先看全量余额，再对命中策略客群与未命中客群做对比
- 可选项：仅全量、仅命中策略客群、全量+命中/未命中对比
- 说明：如果不区分是否命中策略，可能只能看到结果，难以判断是否真由“4W限制放开”带来

### 二、同期业务背景
**问题3：2024年11月前后是否有同步发生的营销活动？**
- 默认答案：无特殊营销活动
- 可选项：免息券活动、折扣券活动、拉新活动、促活活动
- 说明：营销活动会同时影响借款人数、人均借款金额和余额，需确认是否与该策略叠加发生

**问题4：2024年11月前后是否还有其他策略调整与“单笔4W放开”同时发生？**
- 默认答案：无其他同步策略调整
- 可选项：提额、降价/利率调整、风控策略变化、审批策略变化、新名单放量
- 说明：若同期还有额度、利率、风控、审批或名单策略变化，需要与“单笔4W放开”做区分归因

### 三、分析重点与深度
**问题5：本次优先想回答哪类影响？**
- 默认答案：优先看余额是否被正向拉动，以及主要是人数驱动还是金额驱动
- 可选项：规模拉动、波动解释、客群结构变化、策略归因
- 说明：该问题决定输出重点是“余额增了多少”，还是进一步解释“为什么增、由谁贡献”

**问题6：是否需要深入拆到首借/存量两类客群分别看影响？**
- 默认答案：是，需要拆分首借和存量
- 可选项：是、否
- 说明：单笔限额放开可能主要影响存量客户加借，也可能带动首借客单笔提升；不拆分会影响结论准确性

**问题7：是否需要继续下钻到行为层，区分“借得更多”还是“留得更久”？**
- 默认答案：是，需要继续拆解
- 可选项：是、否
- 说明：若仅看余额，无法判断是笔均/人均借款金额提升，还是提前结清减少、新借据留存变好所致
第一层：总量层分析  
📈 先看 2024 年 11 月放开单笔 4W 限制前后，贷款余额的月度趋势、环比变化和变化幅度，判断是否出现明显拐点或增速抬升。  
📊 同步看新增余额、有余额客户数、人均余额三项是否同步变化，区分是“更多客户贡献”还是“单客借得更多”带来的余额变化。  
🎯 建议结合政策生效前后 2-3 个月做对比，避免把季节性波动误判为策略影响。  
→ 先回答“余额有没有明显变化、变化有多大、是人数驱动还是金额驱动”。  

第二层：客群结构层分析  
👥 按客户生命周期拆解为首借客户余额、存量客户余额，判断放开单笔大额后，影响主要来自新客首借还是老客复借/加借。  
📊 按额度分段、利率分段、地区/渠道等维度看结构变化，重点观察高额度客群、高使用率客群的余额贡献是否提升。  
🎯 将“放开单笔大额”作为策略维度切分，比较命中该策略客群与未命中客群的余额变化差异。  
→ 判断“是哪类客群、哪类结构”承接了单笔限额放开的影响。  

第三层：行为拆解层分析  
📈 如果是首借侧贡献上升，重点拆解“首借客户数 × 人均借款金额 × 新借据留存率”，判断是首借人数增加，还是单笔/人均借款金额抬升。  
📊 如果是存量侧贡献上升，重点拆解“新发放借据余额 − 提前还款流失余额”，判断是新增借款变多，还是提前结清减少。  
💳 针对本次策略本身，重点关注笔均借款金额、人均借款金额、额度使用率是否上升，以及是否带来更高的新借据余额留存。  
→ 核心是确认：单笔 4W 限制放开，到底影响了“借多少”，还是也影响了“留多久”。  

第四层：指标联动层分析  
📉 若首借变化明显，继续看开通未借人数、来访率、申请率、批核率，排除其实是流量或审批策略变化带来的间接影响。  
📊 若存量变化明显，继续看借款客户数、静止时间分段、提额/降价、老借据提前结清率，判断是否有其他策略同时共振。  
🎯 特别关注高额度客群是否出现“单笔金额上升但余额未明显增长”的情况，这可能意味着客户只是提款更集中、但未改变整体借款需求或留存。  
→ 只有当金额类指标上升且留存/提前结清没有明显对冲时，才能认为放开单笔大额对余额有正向拉动。
3、回答不符合预期
微信渠道今日较昨日余额变化了多少？
不仅回答了余额是多少和变化，还会进一步拆分变化以及变化原因定位。后续需要做意图识别，对于简单问题（或模糊无法识别的问题）仅答复是什么，不去进一步分析为什么。
第一层：总量层分析
📊 微信渠道今日贷款余额绝对值
📈 微信渠道今日较昨日余额变化（绝对值 & 环比率）
🎯 余额变化方向判断（增长/下降/持平）
→ 判断微信渠道余额变化是否显著（日环比±2%以上视为显著）

第二层：客群结构层分析
👥 首借客户余额：微信渠道当日首次借款客户的余额贡献
👥 存量客户余额：微信渠道已有借款记录客户的余额贡献
📊 两类客群的余额变化及占比变化
→ 拆解首借客户 vs 存量客户的贡献度，定位主要驱动客群

第三层：行为拆解层
针对主要贡献客群深入拆解：
- 首借客户：新借据发放额 × 留存率
  - 发放额 = 首借客户数 × 人均借款金额
  - 留存率 = 1 - 新借据当日提前结清率
- 存量客户：新发放借据余额 - 提前还款流失余额
  - 新借据余额 = 发放额 × 留存率
  - 发放额 = 借款人数 × 人均借款金额
→ 定位是"量"的问题（客户数/借款人数）还是"率"的问题（留存率/提前结清率）

第四层：渠道特征层（可选）
🔍 微信渠道来访-营销渠道明细分析
🎁 微信渠道营销用券使用情况
📱 微信渠道与其他渠道（手Q等）的对比
→ 识别微信渠道特有的营销动作或策略调整影响
看看上个季度的余额走势？
在分析思路中是按照上个季度的余额趋势走势分析的，但是拆解分析框架时，只拆解了12月相关的数据
第一层：总量趋势层分析  
📈 聚焦上个季度贷款余额的整体走势，观察季度内各月余额是持续上升、持续回落，还是先升后降  
📊 对比上个季度各月余额的环比变化幅度，同时结合季度初与季度末的余额差值判断整体变化强弱  
🎯 结合近几个季度或近12个月背景，判断上季度走势是否属于季节性波动，还是出现了明显异常变化  
→ 先确认“上个季度余额是增长、下降还是震荡”，以及变化幅度是否显著。  

第二层：客群结构层分析  
👥 将上个季度余额拆分为首借客户余额与存量客户余额，判断是哪一类客群主导了季度走势  
📊 观察首借客户、存量客户在季度内的余额趋势、环比变化及占比变化，看结构是否发生迁移  
🔍 如果总量走势出现拐点，重点看是否由某一客群在某个月份明显放大或拖累  
→ 明确余额走势背后“是谁在贡献变化”，是首借拉动，还是存量波动主导。  

第三层：行为拆解层分析  
📈 若首借客户是重点来源，可进一步看“新借据发放额 × 留存率”，判断是首借规模变化还是当月留存变化带动余额波动  
📊 若存量客户是重点来源，可拆解为“新发放借据余额 - 提前还款流失余额”，判断新增投放是否覆盖了还款流失  
💡 同步关注新借据提前结清、老借据提前结清等行为，识别是否存在提前还款加快导致余额承压的情况  
→ 将季度走势落到具体业务动作上，定位是“新增变了”还是“流失变了”。  

第四层：关键指标联动层  
👥 首借侧可联动看开通未借人数、来访率、申请率、批核率、首借客户数，判断首借余额变化是否来自漏斗转化变化  
📈 存量侧可联动看借款客户数、人均借款金额、新旧借据提前结清率，判断存量余额变化是人数问题、金额问题还是还款问题  
🎯 如有需要，可进一步按额度分段、利率分段、地区、渠道、静止时间分段等维度下钻，定位具体结构性来源  
→ 最终判断上季度余额走势的核心驱动，是量的变化、率的变化，还是客群结构变化。
2025年12月贷款余额变化分析框架
-- 1 第一层：总量层分析（判断问题“有没有 & 多大”）
---- 1.1 最近24个月贷款余额整体趋势（2024.01-2025.12，所有渠道，所有产品版本）
---- 1.2 2025年12月贷款余额绝对值
---- 1.3 环比变化分析（2025.12 vs 2025.11）
------ 1.3.1 绝对值变化（亿元）
------ 1.3.2 环比增长率（%）
---- 1.4 同比变化分析（2025.12 vs 2024.12）
------ 1.4.1 绝对值变化（亿元）
------ 1.4.2 同比增长率（%）
---- 1.5 月内走势补充观察（如支持日粒度）
------ 1.5.1 月初-月中-月末余额变化节奏
------ 1.5.2 是否存在单日跳升或跳降
---- 1.6 变化显著性判断
------ 1.6.1 环比变化是否超过±5%阈值
------ 1.6.2 是否偏离近12个月常规波动区间
---- 1.7 背景定位
------ 1.7.1 2025年12月走势在近6个月中的位置
------ 1.7.2 2025年12月走势在近12个月中的位置
------ 1.7.3 是否符合年末季节性特征

-- 2 第二层：客群结构层分析（判断问题“是谁在贡献变化”）
---- 2.1 总余额按客户生命周期拆解
------ 2.1.1 首借客户余额
------ 2.1.2 存量客户余额
---- 2.2 首借客户余额趋势分析（2025.01-2025.12）
------ 2.2.1 2025年12月首借客户余额绝对值
------ 2.2.2 2025年12月首借客户余额环比变化
------ 2.2.3 首借余额占总余额比重变化
---- 2.3 存量客户余额趋势分析（2025.01-2025.12）
------ 2.3.1 2025年12月存量客户余额绝对值
------ 2.3.2 2025年12月存量客户余额环比变化
------ 2.3.3 存量余额占总余额比重变化
---- 2.4 结构迁移判断
------ 2.4.1 本月总余额变化主要由首借还是存量驱动
------ 2.4.2 若总量上升，判断增量主要来自哪类客群
------ 2.4.3 若总量下降，判断拖累主要来自哪类客群
---- 2.5 客群贡献度归因
------ 2.5.1 首借对总余额环比变化贡献率
------ 2.5.2 存量对总余额环比变化贡献率
------ 2.5.3 是否存在单一客群异常放大或拖累

-- 3 第三层：行为拆解层分析（判断问题“到底是哪种业务动作变了”）
---- 3.1 首借客户余额拆解
------ 3.1.1 首借客户余额 = 当月新借据发放额 × 当月新借据留存率
------ 3.1.2 当月新借据发放额 = 首借客户数 × 首借客户人均借款金额
------ 3.1.3 留存率 = 1 - 新借据当月提前结清率
---- 3.2 首借变化归因
------ 3.2.1 是首借客户数变化驱动
------ 3.2.2 还是首借人均借款金额变化驱动
------ 3.2.3 还是新借据留存率变化驱动

------ 3.2.4 是否存在新借据提前结清加快压缩余额
---- 3.3 存量客户余额拆解
------ 3.3.1 存量客户余额变化 = 新发放借据余额 - 提前还款流失余额
------ 3.3.2 当月新借据余额 = 当月新借据发放额 × 当月新借据留存率
------ 3.3.3 当月新借据发放额 = 借款客户数 × 人均借款金额
---- 3.4 存量变化归因
------ 3.4.1 是借款客户数变化驱动
------ 3.4.2 还是人均借款金额变化驱动
------ 3.4.3 还是新借据当月提前结清率变化驱动
------ 3.4.4 还是老借据提前结清率变化驱动
------ 3.4.5 新增投放是否覆盖提前还款流失
---- 3.5 行为异常定位
------ 3.5.1 新增放款异常
------ 3.5.2 留存异常
------ 3.5.3 提前结清异常
------ 3.5.4 新老借据行为是否同时变化

模糊问题：
截至目前的2026年度微粒贷月日均余额是多少？
这里其实想问年度日均余额，但多加了月的干扰项，能够识别并能和用户确认，找到准确的口径
### 一、结果口径范围
**问题1：你要的“截至目前的2026年度月日均余额”具体按哪种理解输出？**
- 默认答案：优先输出“2026年截至最新可用日期的各月月日均余额序列，并重点给出最新月份月日均余额”
- 可选项：最新月份月日均余额、2026年截至目前累计期间日均余额、两者都要
- 说明：“截至目前的2026年度”常见有两种理解：一是看2026年各月表现中的最新月水平，二是看2026-01-01至最新可用日的累计期间日均余额；两者数值含义不同，需避免混淆

**问题2：分析结果是否只看微粒贷主产品，还是需要包含其特定子客群/子场景口径？**
- 默认答案：仅看微粒贷整体口径
- 可选项：微粒贷整体、指定子客群、指定子场景
- 说明：若业务上存在需单独关注的客群或场景（如特定名单、特定渠道），需提前说明，否则默认按微粒贷整体看

### 二、分析重点
**问题3：本次更希望优先回答哪类问题？**
- 默认答案：先回答“当前最新月月日均余额是多少 + 2026年以来月度走势如何”
- 可选项：当前水平、年度走势、波动解释、结构健康
- 说明：用户原问题首先是“多少”，但简要分析思路还涉及“怎么走、为什么变动”，需确认输出重点，避免展开过深

**问题4：是否需要同步给出2026年以来各月月日均余额的环比变化？**
- 默认答案：是
- 可选项：是、否
- 说明：月日均余额单看绝对值不够，环比有助于判断今年以来是持续增长、回落还是波动运行

### 三、业务背景补充
**问题5：2026年以来（尤其最新月）是否有需要纳入解释的业务动作？**
- 默认答案：无
- 可选项：免息券活动、折扣券活动、新名单放量、额度调整、风控策略变化、审批策略变化
- 说明：若后续不仅回答“多少”，还需要解释“为什么是这个水平”，这些业务动作往往是余额波动的重要背景

### 四、分析深度
**问题6：本次是否需要进一步拆解到首借/存量贡献？**
- 默认答案：否，先看总量
- 可选项：否、是
- 说明：首借/存量拆解适用于解释余额变化来源；若当前仅需回答总量水平，可先不展开

**问题7：如果需要解释波动，是否要继续深入到行为驱动层？**
- 默认答案：否，除非最新月较前几月出现明显异常
- 可选项：否、是
- 说明：行为驱动层通常包括新增借款、提前还款、留存等拆解，适合用于解释异常波动，不是回答当前余额“多少”的必选项
5. 当前面临的问题与解决方案（旭辉）
1、加一个意图识别：对问题进行程度分级，简单问题只需要第一层总量层，回答是什么
2、所有的问题都不要主动提出预测，若提出预测，需要提示仅供参考
3、对于模棱两可的问题都先给简单的回答
4、无论是对于知识库还是skill都可以用户共同开发优化
5、对于事件的问题，先看事件库，如果事件库有，则采用事件库中分析，如果没有则先给出定性分析
6、客群结构的拆解，不应该都拆解，而是按某个主题下拆解，某些主题下不用拆解。
7、加个分析场景的分类，可以选
8、给出来不符合预期的、符合预期的、超预期的、用了通用
问题
解决方案
出现异常的名词
调试提示词
分析思路和分析框架不一致
调试生成分析框架的提示词
对于简单的问答余额，也会同步分析原因

加一个意图识别：对问题进行程度分级，简单问题只需要第一层总量层，回答是什么；对于模棱两可的问题都先给简单的回答


固化的指标没办法解决所有的分析问题；
引入coplite
受权限问题的影响，可能无法访问所有的数据，导致分析不完整
暂无
6. 未来规划
暂时无法在飞书文档外展示此内容