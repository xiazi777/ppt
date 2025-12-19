graph LR
    %% === 样式定义 (仿照原图配色) ===
    classDef backbone fill:#9dc3e6,stroke:#2e5894,color:black;
    classDef csci fill:#ffc000,stroke:#bf9000,color:black;
    classDef dae fill:#70ad47,stroke:#507e32,color:black;
    classDef gmp fill:#8ea9db,stroke:#2f5597,color:black;
    classDef nav fill:#ed7d31,stroke:#c65911,color:black;
    classDef gpfa fill:#ffe699,stroke:#bf9000,color:black;
    classDef cls fill:#deebf7,stroke:#9dc3e6,color:black;
    classDef concat fill:#fff,stroke:#333,stroke-dasharray: 5 5,color:black;

    %% === 主干网络 ===
    Img[原始图像] --> Backbone
    subgraph Global_Stream [Global Stream / 全局分支]
        direction LR
        Backbone(Backbone):::backbone
        
        %% CSCI 模块 (跨层交互)
        Backbone -- Low & Mid Feat --> CSCI1[CSCI]:::csci
        Backbone -- Mid & High Feat --> CSCI2[CSCI]:::csci
        
        %% DAE 模块 (细节增强)
        CSCI1 --> DAE1[DAE]:::dae
        CSCI2 --> DAE2[DAE]:::dae
        Backbone -- High Feat --> DAE3[DAE]:::dae
        
        %% GMP (池化)
        DAE1 --> GMP1[GMP]:::gmp
        DAE2 --> GMP2[GMP]:::gmp
        DAE3 --> GMP3[GMP]:::gmp
        
        %% 分类器 1-3
        GMP1 --> C1[Classifier1]:::cls
        GMP2 --> C2[Classifier2]:::cls
        GMP3 --> C3[Classifier3]:::cls
        
        %% 融合分类器 4
        GMP1 & GMP2 & GMP3 --> Concat((Concat)):::concat
        Concat --> C4[Classifier4]:::cls
    end

    %% === 导航与局部分支 ===
    subgraph Local_Stream [Local Stream / 局部分支]
        direction LR
        
        %% 导航器
        Backbone --> Navigator[Navigator]:::nav
        Navigator -.-> Crop[根据Box裁剪]
        Crop --> Parts[局部图像 Parts]
        
        %% 局部特征提取
        Parts --> LocalBB(Backbone\n共享权重):::backbone
        LocalBB --> GPFA[GPFA\n特征聚合]:::gpfa
        GPFA --> C5[Classifier5]:::cls
    end
    
    %% 调整连接线样式以减少混乱
    linkStyle default stroke-width:2px,fill:none,stroke:#555;
