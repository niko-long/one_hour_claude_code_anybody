# Workshop 实战示例：tidydown

一个命令行工具，自动整理 Downloads 文件夹，按文件类型分类归档。

做完就能用在自己电脑上，每个人的 Downloads 都是一团糟。

---

## 为什么做这个

打开你的 Downloads 文件夹看一眼，大概率长这样：

```
~/Downloads/
├── IMG_4521.jpg
├── IMG_4522.jpg
├── report-q1.pdf
├── report-q1 (1).pdf
├── screenshot 2026-03-15.png
├── 合同模板.docx
├── node-v22.pkg
├── archive.zip
├── meeting-notes.md
├── 发票.xlsx
├── video-demo.mp4
├── ... (还有 200 个文件)
```

运行一次 `tidydown` 之后：

```
~/Downloads/
├── images/
│   ├── IMG_4521.jpg
│   ├── IMG_4522.jpg
│   └── screenshot 2026-03-15.png
├── documents/
│   ├── report-q1.pdf
│   ├── report-q1 (1).pdf
│   ├── 合同模板.docx
│   ├── meeting-notes.md
│   └── 发票.xlsx
├── videos/
│   └── video-demo.mp4
├── archives/
│   └── archive.zip
├── installers/
│   └── node-v22.pkg
└── other/
    └── (无法识别类型的文件)
```

---

## 准备工作

```bash
# 创建项目目录
mkdir tidydown && cd tidydown

# 初始化 Git
git init

# 创建一个模拟的乱 Downloads 文件夹用于测试（不要动真的 Downloads）
mkdir -p test_downloads
touch test_downloads/{photo1.jpg,photo2.png,screenshot.gif}
touch test_downloads/{report.pdf,notes.md,resume.docx,data.xlsx,slides.pptx}
touch test_downloads/{demo.mp4,clip.mov,song.mp3}
touch test_downloads/{backup.zip,project.tar.gz}
touch test_downloads/{installer.pkg,app.dmg}
touch test_downloads/{random.xyz,mystery.abc}

# 启动 Claude Code
claude
```

---

## Step 1 · 要方案，不要代码

```
你：
我要做一个 Python CLI 工具叫 tidydown，功能是整理文件夹里的文件，
按文件类型自动归类到子目录。

技术要求：
- Python 3.12，只用标准库（pathlib, shutil, argparse）
- 不用第三方依赖

文件分类规则：
- images: jpg, jpeg, png, gif, webp, svg, ico, bmp, heic
- documents: pdf, doc, docx, xls, xlsx, ppt, pptx, txt, md, csv, rtf
- videos: mp4, mov, avi, mkv, wmv, flv
- audio: mp3, wav, flac, aac, ogg, m4a
- archives: zip, tar, gz, rar, 7z, tar.gz, bz2
- installers: pkg, dmg, exe, msi, deb, rpm
- code: py, js, ts, html, css, json, xml, yaml, yml, sql, sh
- other: 所有无法识别的文件

功能：
1. 指定目标文件夹路径（默认 ~/Downloads）
2. --dry-run 模式：只显示会怎么移动，不实际操作
3. 移动前显示操作计划，让用户确认
4. 处理文件名冲突（目标已存在同名文件时自动加序号）

先告诉我项目结构和实现思路，不要写代码。
```

等 Claude 给出方案，看一下是否合理，确认后继续。

---

## Step 2 · 初始化项目

```
你：
方案可以。先创建项目结构和 tidydown.py 的骨架。
包含 argparse 参数解析和分类规则的常量定义。
核心函数先留空，写好 docstring 和类型注解。
```

预期产出：

```python
# tidydown.py
from pathlib import Path
import argparse
import shutil

# 文件类型分类规则
CATEGORIES: dict[str, list[str]] = {
    "images": ["jpg", "jpeg", "png", "gif", "webp", "svg", "ico", "bmp", "heic"],
    "documents": ["pdf", "doc", "docx", "xls", "xlsx", "ppt", "pptx", "txt", "md", "csv", "rtf"],
    "videos": ["mp4", "mov", "avi", "mkv", "wmv", "flv"],
    "audio": ["mp3", "wav", "flac", "aac", "ogg", "m4a"],
    "archives": ["zip", "tar", "gz", "rar", "7z", "bz2"],
    "installers": ["pkg", "dmg", "exe", "msi", "deb", "rpm"],
    "code": ["py", "js", "ts", "html", "css", "json", "xml", "yaml", "yml", "sql", "sh"],
}
DEFAULT_CATEGORY = "other"


def classify_file(filepath: Path) -> str:
    """根据扩展名返回分类名称。"""
    ...


def scan_folder(folder: Path) -> dict[str, list[Path]]:
    """扫描文件夹，返回 {分类: [文件列表]} 的字典。"""
    ...


def resolve_conflict(target: Path) -> Path:
    """目标路径已存在时，返回加序号的新路径。"""
    ...


def organize(folder: Path, dry_run: bool = False) -> None:
    """执行整理操作。"""
    ...


def main():
    parser = argparse.ArgumentParser(description="Tidy up your Downloads folder")
    parser.add_argument("folder", nargs="?", default="~/Downloads",
                        help="Folder to organize (default: ~/Downloads)")
    parser.add_argument("--dry-run", action="store_true",
                        help="Show what would be moved without actually moving")
    ...
```

**Review diff，确认，提交：**

```
你：
提交，message "scaffold project with file category definitions"
```

---

## Step 3 · 实现分类和扫描

```
你：
实现 classify_file 和 scan_folder 两个函数。

注意事项：
- 扩展名统一转小写再匹配
- 跳过目录（只处理文件）
- 跳过隐藏文件（以 . 开头的）
- .tar.gz 这种双扩展名要能正确识别为 archives
- 已经在子分类目录里的文件不要重复处理
```

Review 后提交：

```
你：
提交，message "implement file classification and folder scanning"
```

---

## Step 4 · 实现移动逻辑

```
你：
实现 resolve_conflict 和 organize 函数。

organize 的流程：
1. 调用 scan_folder 获取分类结果
2. 打印操作计划（每个分类有多少文件，列出文件名）
3. 如果是 dry_run 模式，打印后直接返回
4. 如果不是 dry_run，提示用户确认（y/n）
5. 确认后创建子目录并移动文件
6. 移动完成后打印总结（移动了多少文件到多少个目录）

resolve_conflict 逻辑：
- report.pdf 已存在 → 改为 report_1.pdf
- report_1.pdf 也存在 → 改为 report_2.pdf
- 依此类推
```

Review 后提交：

```
你：
提交，message "implement file moving with conflict resolution"
```

---

## Step 5 · 用测试文件夹试一下

```
你：
先用 --dry-run 模式在 test_downloads 文件夹上跑一下，让我看看输出。
```

预期看到类似的输出：

```
Scanning: test_downloads/

Plan:
  images/     → 3 files (photo1.jpg, photo2.png, screenshot.gif)
  documents/  → 5 files (report.pdf, notes.md, resume.docx, data.xlsx, slides.pptx)
  videos/     → 2 files (demo.mp4, clip.mov)
  audio/      → 1 file  (song.mp3)
  archives/   → 2 files (backup.zip, project.tar.gz)
  installers/ → 2 files (installer.pkg, app.dmg)
  other/      → 2 files (random.xyz, mystery.abc)

Total: 17 files → 7 folders
(dry run — no files were moved)
```

确认输出正确后，实际执行一次：

```
你：
去掉 --dry-run，实际整理 test_downloads，然后 ls 每个子目录让我看结果。
```

---

## Step 6 · 写测试

```
你：
在 tests/test_tidydown.py 写 pytest 测试，用 tmp_path fixture。

覆盖以下场景：
1. classify_file 对各类扩展名的判断（包括大写扩展名 .JPG）
2. classify_file 对 .tar.gz 双扩展名的处理
3. classify_file 对未知扩展名返回 "other"
4. scan_folder 跳过隐藏文件
5. scan_folder 跳过子目录
6. resolve_conflict 的序号递增逻辑
7. organize 的完整流程（文件确实被移动到正确目录）
8. organize dry_run 模式不移动文件
```

---

## Step 7 · 跑测试并修复

```
你：
运行 pytest -v，如果有失败的测试就修复，直到全部通过。
```

Claude 会进入 Agent 循环：跑测试 → 看报错 → 改代码 → 再跑 → 直到通过。

全部通过后提交：

```
你：
提交，message "add comprehensive tests"
```

---

## Step 8 · 收尾

```
你：
做三件事：
1. 加一个 --undo 功能：把子目录里的文件全部移回父目录并删除空的子目录
   （这个很重要，让用户敢用这个工具）
2. 生成 README.md，包含安装和使用示例
3. 检查类型注解和 docstring
```

分两个 commit 提交：

```
你：
feature 和 docs 分开提交
```

---

## 最终验证

```bash
# 查看 Git 历史
git log --oneline

# 重建测试目录
mkdir -p test_downloads
touch test_downloads/{a.jpg,b.pdf,c.mp4,d.zip,e.xyz}

# dry run
python tidydown.py test_downloads --dry-run

# 实际整理
python tidydown.py test_downloads

# 后悔了？撤销
python tidydown.py test_downloads --undo

# 跑测试
pytest -v
```

---

## 回到真实的 Downloads

当你对工具有信心后，可以在自己的 Downloads 上试：

```bash
# 先 dry run 看看
python tidydown.py ~/Downloads --dry-run

# 确认没问题再执行
python tidydown.py ~/Downloads
```

---

## 过程中用到的 Prompt 技巧

| 技巧 | 体现在哪里 |
|------|-----------|
| 先要方案再写代码 | Step 1 完整描述需求，要求只给思路 |
| 明确技术约束 | "只用标准库，不用第三方依赖" |
| 给出具体的分类规则 | 列出每个分类对应的扩展名 |
| 指定边界情况 | ".tar.gz 双扩展名"、"跳过隐藏文件" |
| 分步推进 | 分类 → 移动 → 测试 → 收尾，每步提交 |
| 让 Agent 自主调试 | Step 7 让 Claude 自己跑测试改 bug |
| 要求可撤销 | --undo 功能，降低用户心理门槛 |

---

## 如果还有时间

- 加 `--by-date` 模式：按修改日期归类（2026-03/、2026-02/）
- 加 `--min-size` 参数：只整理大于指定大小的文件
- 加日志文件：记录每次操作，方便审计
- 支持自定义分类规则（读 YAML 配置文件）
- 打包成 `pip install tidydown` 可安装的工具
