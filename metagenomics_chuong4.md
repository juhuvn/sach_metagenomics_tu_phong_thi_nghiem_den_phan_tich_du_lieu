# Chương 4: Phân tích Metagenomics 16S rRNA với QIIME2
## Cài đặt QIIME2
### Bước 1: Cài đặt Miniconda
- Làm theo hướng dẫn trên trang web https://conda.io/projects/conda/en/latest/user-guide/install/index.html
- Dùng lệnh ```conda init``` để tích hợp conda vào shell
- Dùng ```conda update conda``` để cập nhật conda lên phiên bản mới nhất
### Bước 2: Tạo môi trường Conda và Cài đặt QIIME 2
#### Linux
```
# Thay thế URL bằng link.yml cho phiên bản Linux mới nhất từ docs.qiime2.org
wget https://data.qiime2.org/distro/amplicon/qiime2-amplicon-2024.10-py310-linux-conda.yml
conda env create -n qiime2-amplicon-2024.10 --file qiime2-amplicon-2024.10-py310-linux-conda.yml

# Xóa tệp yml sau khi cài đặt (tùy chọn)
rm qiime2-amplicon-2024.10-py310-linux-conda.yml
```
#### macOS (Intel x86_64)
```
# Thay thế URL bằng link.yml cho phiên bản macOS Intel mới nhất
wget https://data.qiime2.org/distro/amplicon/qiime2-amplicon-2024.10-py310-osx-conda.yml
conda env create -n qiime2-amplicon-2024.10 --file qiime2-amplicon-2024.10-py310-osx-conda.yml

# Xóa tệp yml sau khi cài đặt (tùy chọn)
rm qiime2-amplicon-2024.10-py310-osx-conda.yml
```
#### macOS (Apple Silicon - M1/M2/M3…)
```
# Thay thế URL bằng link.yml cho phiên bản macOS mới nhất (thường dùng chung link osx)
CONDA_SUBDIR=osx-64 conda env create -n qiime2-amplicon-2024.10 --file https://data.qiime2.org/distro/amplicon/qiime2-amplicon-2024.10-py310-osx-conda.yml

# Kích hoạt môi trường ngay sau khi tạo
conda activate qiime2-amplicon-2024.10

# Cấu hình môi trường để luôn sử dụng gói osx-64
conda config --env --set subdir osx-64

# Không cần xóa tệp yml vì không tải về trực tiếp
```
#### Windows (thông qua WSL - Windows Subsystem for Linux)
```
# Chạy trong terminal WSL

# Thay thế URL bằng link.yml cho phiên bản Linux mới nhất
wget https://data.qiime2.org/distro/amplicon/qiime2-amplicon-2024.10-py310-linux-conda.yml
conda env create -n qiime2-amplicon-2024.10 --file qiime2-amplicon-2024.10-py310-linux-conda.yml

# Xóa tệp yml sau khi cài đặt (tùy chọn)
rm qiime2-amplicon-2024.10-py310-linux-conda.yml
```
### Bước 3: Kích hoạt Môi trường QIIME 2
```
# Kích hoạt môi trường QIIME2
conda activate qiime2-amplicon-2024.10

# Thoát khỏi môi trường QIIME2, quauy lại môi trường base
conda deactivate
```
### Bước 4: Kiểm tra Cài đặt
```
# Kiểm tra xem qiime2 đã được cài đặt đúng cách chưa
qiime --help

# Xem chi tiết phiên bản QIIME 2 và các plugin đã cài đặt
qiime info
```
## Thực hành phân tích dữ liệu “Moving Pictures”
### Bước 1: Chuẩn bị dữ liệu: Sample metadata và Sequencing Data
```
# Chuẩn bị thư mục dự án Moving Pictures
mkdir -p ~/qiime2-moving-pictures/data/raw_fastq
cd ~/qiime2-moving-pictures
```
- Tải dữ liệu cần thiết:
    - Cách 1: Dùng trình duyệt tải về từ các đường link bên dưới và lưu vào đúng thư mục của dự án.
    ```
    https://data.qiime2.org/2024.10/tutorials/moving-pictures/sample_metadata.tsv. Save as: data/sample-metadata.tsv
    https://data.qiime2.org/2024.10/tutorials/moving-pictures/emp-single-end-sequences/barcodes.fastq.gz. Save as: data/raw_fastq/barcodes.fastq.gz
    https://data.qiime2.org/2024.10/tutorials/moving-pictures/emp-single-end-sequences/sequences.fastq.gz. Save as: data/raw_fastq/sequences.fastq.gz
    ```
    - Cách 2: Dùng công cụ wget
    ```
    # Tải file sample-metadata.tsv về thư mục data/
    wget \
        -O "data/sample-metadata.tsv" \
    "https://data.qiime2.org/2024.10/tutorials/moving-pictures/sample_metadata.tsv"

    # Tải file sequences.fastq.gz và barcodes.fastq.gz về thư mục data/raw_fastq/
    wget \
        -O "data/raw_fastq/barcodes.fastq.gz" \
    "https://data.qiime2.org/2024.10/tutorials/moving-pictures/emp-single-end-sequences/barcodes.fastq.gz"

    wget \
        -O "data/raw_fastq/sequences.fastq.gz" \
    "https://data.qiime2.org/2024.10/tutorials/moving-pictures/emp-single-end-sequences/sequences.fastq.gz"
    ```
    - Cách 3: Dùng công cụ curl
    ```
    # Tải file sample-metadata.tsv về thư mục data/
    curl -sL \
        "https://data.qiime2.org/2024.10/tutorials/moving-pictures/sample_metadata.tsv" \
        > "data/sample-metadata.tsv"

    # Tải file sequences.fastq.gz và barcodes.fastq.gz về thư mục data/raw_fastq/
    curl -sL "https://data.qiime2.org/2024.10/tutorials/moving-pictures/emp-single-end-sequences/sequences.fastq.gz" > "data/raw_fastq/sequences.fastq.gz"
    curl -sL "https://data.qiime2.org/2024.10/tutorials/moving-pictures/emp-single-end-sequences/barcodes.fastq.gz" > "data/raw_fastq/barcodes.fastq.gz"
    ```
- Kiểm tra dữ liệu sau khi tải:
```
tree ./
```
- Kết quả cần có như sau:
./
└── data/
    ├── raw_fastq/
    │   ├── barcodes.fastq.gz
    │   └── sequences.fastq.gz
    └── sample-metadata.tsv
### Bước 2: Nhập dữ liệu vào QIIME 2
```
# Tạo thư mục artifacts để lưu các file .qza
mkdir -p artifacts

# Nhập dữ liệu vào QIIME2
qiime tools import \
    --type EMPSingleEndSequences \
    --input-path data/raw_fastq/ \
    --output-path artifacts/sequences.qza  
```
### Bước 3: Tách mẫu (Demultiplexing)
```
# Tách mẫu
qiime demux emp-single \
    --i-seqs artifacts/sequences.qza \
    --m-barcodes-file data/sample-metadata.tsv \
    --m-barcodes-column barcode-sequence \
    --o-per-sample-sequences artifacts/demux.qza \
    --o-error-correction-details artifacts/demux-details.qza

# Tạo thư mục visualizations chứa các file .qzv
mkdir -p visualizations

# Trực quan hóa kết quả
qiime demux summarize \
    --i-data artifacts/demux.qza \
    --o-visualization visualizations/demux.qzv
```
### Bước 4: Xử lý nhiễu và tạo Feature Table (DADA2)
```
# Thực hiện xử lý nhiễu và tạo Feature Table
qiime dada2 denoise-single \
    --i-demultiplexed-seqs artifacts/demux.qza \
    --p-trim-left 0 \
    --p-trunc-len 120 \
    --o-representative-sequences artifacts/rep-seqs.qza \
    --o-table artifacts/table.qza \
    --o-denoising-stats artifacts/stats.qza

# Thống kê kết quả Denoising
qiime metadata tabulate \
    --m-input-file artifacts/stats.qza \
    --o-visualization visualizations/stats.qzv

# Xem kết quả thống kê
qiime tools view visualizations/stats.qzv
```
### Bước 5: Tóm tắt ASV table và dãy đại diện (representative sequences)
```
# Tóm tắt ASV Table
qiime feature-table summarize \
    --i-table artifacts/table.qza \
    --o-visualization visualizations/table.qzv \
    --m-sample-metadata-file data/sample-metadata.tsv

# Tóm tắt Representative Sequence
qiime feature-table tabulate-seqs \
    --i-data artifacts/rep-seqs.qza \
    --o-visualization visualizations/rep-seqs.qzv

# Xem kết quả tóm tắt
qiime tools view visualizations/table.qzv
qiime tools view visualizations/rep-seqs.qzv
```
### Bước 6: Xây dựng cây phát sinh chủng loài
```
qiime phylogeny align-to-tree-mafft-fasttree \
    --i-sequences artifacts/rep-seqs.qza \
    --o-alignment artifacts/aligned-rep-seqs.qza \
    --o-masked-alignment artifacts/masked-aligned-rep-seqs.qza \
    --o-tree artifacts/unrooted-tree.qza \
    --o-rooted-tree artifacts/rooted-tree.qza
```
### Bước 7: Phân tích Alpha/Beta Diversity
```
# Phân tích alpha/beta diversity và lưu trong thư mục core-metrics-results
qiime diversity core-metrics-phylogenetic \
    --i-phylogeny artifacts/rooted-tree.qza \
    --i-table artifacts/table.qza \
    --p-sampling-depth 1103 \
    --m-metadata-file data/sample-metadata.tsv \
    --output-dir core-metrics-results

# Tạo visualization cho Faith Phylogenetic Diversity
qiime diversity alpha-group-significance \
    --i-alpha-diversity core-metrics-results/faith_pd_vector.qza \
    --m-metadata-file data/sample-metadata.tsv \
    --o-visualization core-metrics-results/faith-pd-group-significance.qzv

# Xem kết quả Faith Phylogenetic Diversity
qiime tools view core-metrics-results/faith-pd-group-significance.qzv

# Tạo visualization cho Evenness
qiime diversity alpha-group-significance \
    --i-alpha-diversity core-metrics-results/evenness_vector.qza \
    --m-metadata-file data/sample-metadata.tsv \
    --o-visualization core-metrics-results/evenness-group-significance.qzv

# Xem kết quả Evenness
qiime tools view core-metrics-results/evenness-group-significance.qzv

# So sánh khoảng cách giữa các nhóm gut, tongue, left palm, right palm
qiime diversity beta-group-significance \
    --i-distance-matrix core-metrics-results/unweighted_unifrac_distance_matrix.qza \
    --m-metadata-file data/sample-metadata.tsv \
    --m-metadata-column body-site \
    --o-visualization core-metrics-results/unweighted-unifrac-body-site-significance.qzv \
    --p-pairwise

# Xem kết quả so sánh khoảng cách giữa các nhóm gut, tongue, left palm, right palm
qiime tools view \
    core-metrics-results/unweighted-unifrac-body-site-significance.qzv

# So sánh khoảng cách giữa các đối tượng nghiên cứu
qiime diversity beta-group-significance \
  --i-distance-matrix core-metrics-results/unweighted_unifrac_distance_matrix.qza \
  --m-metadata-file data/sample-metadata.tsv \
  --m-metadata-column subject \
  --o-visualization core-metrics-results/unweighted-unifrac-subject-group-significance.qzv \
  --p-pairwise

# Xem kết quả so sánh khoảng cách giữa các đối tượng nghiên cứu
qiime tools view \
    core-metrics-results/unweighted-unifrac-subject-group-significance.qzv

# Vẽ biểu đồ Emperor cho khoảng cách unweighted UniFrac
qiime emperor plot \
    --i-pcoa core-metrics-results/unweighted_unifrac_pcoa_results.qza \
    --m-metadata-file data/sample-metadata.tsv \
    --p-custom-axes days-since-experiment-start \
    --o-visualization core-metrics-results/unweighted-unifrac-emperor-days-since-experiment-start.qzv

# Xem biểu đồ Emperor cho khoảng cách unweighted UniFrac
qiime tools view core-metrics-results/unweighted-unifrac-emperor-days-since-experiment-start.qzv

# Vẽ biểu đồ Emperor cho khoảng cách Bray-Curtis 
qiime emperor plot \
    --i-pcoa core-metrics-results/bray_curtis_pcoa_results.qza \
    --m-metadata-file data/sample-metadata.tsv \
    --p-custom-axes days-since-experiment-start \
    --o-visualization core-metrics-results/bray-curtis-emperor-days-since-experiment-start.qzv

# Xem biểu đồ Emperor cho khoảng cách Bray-Curtis
qiime tools view core-metrics-results/bray-curtis-emperor-days-since-experiment-start.qzv
```
### Bước 8: Vẽ đường cong Alpha Rarefaction
```
# Vẽ biểu đồ đường cong Alpha Rarefaction
qiime diversity alpha-rarefaction \
    --i-table artifacts/table.qza \
    --i-phylogeny artifacts/rooted-tree.qza \
    --p-max-depth 4000 \
    --m-metadata-file data/sample-metadata.tsv \
    --o-visualization visualizations/alpha-rarefaction.qzv

# Xem biểu đồ đường cong Alpha Rarefaction
qiime tools view visualizations/alpha-rarefaction.qzv
```
### Bước 9: Gán phân loại học (Taxonomy)
- Tải về bộ phân loại Greengenes 13_8 99% OTUs
    - Cách 1: Dùng trình duyệt tải về và lưu vào thư mục tương ứng
    ```
    https://data.qiime2.org/classifiers/sklearn-1.4.2/greengenes/gg-13-8-99-515-806-nb-classifier.qza 
    Save as: artifacts/gg-13-8-99-515-806-nb-classifier.qza
    ```
    - Cách 2: Dùng wget
    ```
    wget \
    -O "artifacts/gg-13-8-99-515-806-nb-classifier.qza" \
    "https://data.qiime2.org/classifiers/sklearn-1.4.2/greengenes/gg-13-8-99-515-806-nb-classifier.qza"
    ```
    - Cách 3: Dùng curl
    ```
    curl -sL \
    "https://data.qiime2.org/classifiers/sklearn-1.4.2/greengenes/gg-13-8-99-515-806-nb-classifier.qza" > \
    "artifacts/gg-13-8-99-515-806-nb-classifier.qza"
    ```
- Gán Taxonomy với câu lệnh sau:
```
# Gán phân loại học Taxonomy
qiime feature-classifier classify-sklearn \
    --i-classifier artifacts/gg-13-8-99-515-806-nb-classifier.qza \
    --i-reads artifacts/rep-seqs.qza \
    --o-classification artifacts/taxonomy.qza

# Tạo visualization
qiime metadata tabulate \
    --m-input-file artifacts/taxonomy.qza \
    --o-visualization visualizations/taxonomy.qzv

# Xem visualization
qiime tools view visualizations/taxonomy.qzv
```
- Xây dựng biểu đồ cột để xem thành phần phân loại học của các mẫu
```
# Tạo visualization
qiime taxa barplot \
    --i-table artifacts/table.qza \
    --i-taxonomy artifacts/taxonomy.qza \
    --m-metadata-file data/sample-metadata.tsv \
    --o-visualization visualizations/taxa-bar-plots.qzv

# Xem visualization
qiime tools view visualizations/taxa-bar-plots.qzv
```
### Bước 10: Kiểm định sự khác biệt mức độ phong phú (ANCOM-BC)
```
# Tạo một bảng đặc điểm chỉ chứa các mẫu ruột
qiime feature-table filter-samples \
    --i-table artifacts/table.qza \
    --m-metadata-file data/sample-metadata.tsv \
    --p-where "[body-site]='gut'" \
    --o-filtered-table artifacts/gut-table.qza

# Xác định những đặc điểm nào có độ phong phú khác biệt giữa các mẫu ruột của hai chủ thể
qiime composition ancombc \
  --i-table artifacts/gut-table.qza \
  --m-metadata-file data/sample-metadata.tsv \
  --p-formula 'subject' \
  --o-differentials artifacts/ancombc-subject.qza

# Xây dựng visualization
qiime composition da-barplot \
  --i-data artifacts/ancombc-subject.qza \
  --p-significance-threshold 0.001 \
  --o-visualization visualizations/da-barplot-subject.qzv

# Xem visualization
qiime tools view visualizations/da-barplot-subject.qzv

# Gộp bảng đặc điểm theo mức độ phân loài là genus
qiime taxa collapse \
  --i-table gut-table.qza \
  --i-taxonomy taxonomy.qza \
  --p-level 6 \
  --o-collapsed-table gut-table-l6.qza

# Xác định những đặc điểm nào có độ phong phú khác biệt giữa các mẫu ruột thuộc mức độ phân loài là chi
qiime composition ancombc \
  --i-table gut-table-l6.qza \
  --m-metadata-file sample-metadata.tsv \
  --p-formula 'subject' \
  --o-differentials l6-ancombc-subject.qza

# Xây dựng visualization
qiime composition da-barplot \
  --i-data l6-ancombc-subject.qza \
  --p-significance-threshold 0.001 \
  --p-level-delimiter ';' \
  --o-visualization l6-da-barplot-subject.qzv
```