import os
import re
import requests
from bs4 import BeautifulSoup

def download_webtoon_from_html(file_path, base_img_url, max_pages):
    try:
        with open(file_path, 'r', encoding='utf-8') as f:
            html_content = f.read()
        
        soup = BeautifulSoup(html_content, 'html.parser')
        links = soup.find_all('a', class_='ep-row-v2-link')
        
        print(f"\n✅ 분석 완료. 다운로드를 시작합니다. (최대 {max_pages}장)")
        print("-" * 60)

        for link in links:
            href = link.get('href', '')
            # URL에서 회차 ID 추출
            match = re.search(r'/webtoon/파일이름/(\d+)', href)
            if not match:
                continue
            
            ep_id = match.group(1)

            # 회차 제목 추출
            title_tag = link.find('div', class_='ep-row-v2-title')
            title = title_tag.strong.get_text(strip=True) if title_tag and title_tag.strong else "제목없음"
            
            # 폴더명 생성
            clean_title = re.sub(r'[\\/:*?"<>|]', '', title)
            folder_name = f"{clean_title}_{ep_id}"
            
            if not os.path.exists(folder_name):
                os.makedirs(folder_name)
                print(f"\n📂 폴더 생성: {folder_name}")
            else:
                print(f"\n📂 폴더 확인: {folder_name}")

            # --- 이미지 다운로드 반복문 ---
            for i in range(1, max_pages + 1):
                img_name = f"p{i:03d}.jpg" 
                img_url = f"{base_img_url}/15626/{ep_id}/{img_name}"
                save_path = os.path.join(folder_name, img_name)

                # [추가] 파일이 이미 존재하는지 확인
                if os.path.exists(save_path):
                    print(f"   ⏩ {img_name} 이미 있음 (건너뜀)")
                    continue

                try:
                    headers = {'User-Agent': 'Mozilla/5.0'}
                    res = requests.get(img_url, headers=headers, stream=True, timeout=10)
                    
                    if res.status_code == 200:
                        with open(save_path, 'wb') as f:
                            f.write(res.content)
                        print(f"   └─ {img_name} 다운로드 완료")
                        print(f"      🔗 주소: {img_url}")
                    else:
                        print(f"   🏁 {img_name} 없음 (HTTP {res.status_code}). 이 회차 종료.")
                        print(f"      🔗 마지막 시도 주소: {img_url}")
                        break
                        
                except Exception as e:
                    print(f"   ⚠️ 에러 발생: {e}")
                    break

        print("\n" + "="*60)
        print("✨ 모든 작업이 종료되었습니다.")
        print("="*60)

    except Exception as e:
        print(f"❌ 오류 발생: {e}")

# --- 실행 부분 ---
if __name__ == "__main__":
    HTML_FILE = '파일이름.html' 
    
    # 1. 이미지 서버 주소 입력
    IMG_BASE = input("🖼️ 이미지 서버 기본 주소를 입력하세요: ").strip()
    
    # 2. 최대 다운로드 장수 입력
    try:
        LIMIT = int(input("🔢 한 회차당 최대 몇 장까지 시도할까요? (예: 200): "))
    except ValueError:
        print("숫자만 입력 가능합니다. 기본값 200으로 설정합니다.")
        LIMIT = 200

    download_webtoon_from_html(HTML_FILE, IMG_BASE, LIMIT)
