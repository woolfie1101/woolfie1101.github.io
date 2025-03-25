---
layout: single
title:  "[Python] tableau user role change"
date: 2025-03-12
categories:
  - blog
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["기타"]
search: true
---

# [Python] tableau user role change

원문: [https://www.notion.so/1b4bf506e994801896c5d2da8fe78cee](https://www.notion.so/1b4bf506e994801896c5d2da8fe78cee)

```python
config = {
    'my_env': {
        'server': 'https://your.tableau.server',
        'api_version': '3.17',
        'username': 'admin@example.com',
        'password': '********',
        'site_name': '',
        'site_url': ''
    }
} 
```

```python
import tableauserclient as TSC
import argparse
import logging
from datetime import datetime
from config import config

# TSC 모델 초기화 (Initialize TSC models)
TSC.models.flow_item

# 로깅 설정 (Set up logging)
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler(f'role_check_{datetime.now().strftime("%Y%m%d_%H%M%S")}.log'),
        logging.StreamHandler()
    ]
)

def connect_to_server():
    """태블로 서버에 연결 (Connect to Tableau Server)"""
    env_config = config['my_env']
    tableau_auth = TSC.TableauAuth(
        username=env_config['username'],
        password=env_config['password'],
        site_id=''
    )
    
    server = TSC.Server(env_config['server'], use_server_version=True)
    
    try:
        server.auth.sign_in(tableau_auth)
        logging.info("서버 연결 성공 (Successfully connected to server)")
        return server
    except Exception as e:
        logging.error(f"서버 연결 실패 (Failed to connect to server): {str(e)}")
        raise

def get_all_users(server):
    """모든 사용자 가져오기 (Fetch all users)"""
    all_users = []
    req_option = TSC.RequestOptions(pagesize=1000)
    
    try:
        for user in TSC.Pager(server.users, req_option):
            all_users.append(user)
        return all_users
    except Exception as e:
        logging.error(f"사용자 목록 조회 실패 (Failed to retrieve user list): {str(e)}")
        raise

def should_update_user(user):
    """사용자가 업데이트 조건을 만족하는지 확인 (Check if user meets update criteria)"""
    # 1. 현재 역할이 'ExplorerCanPublish'인지 확인 (Check if current role is 'ExplorerCanPublish')
    if user.site_role != 'ExplorerCanPublish':
        return False
    
    # 2. 사용자 이름이 @example.com으로 끝나는지 확인 (Check if email ends with @example.com)
    if not user.name.lower().endswith('@example.com'):
        return False
    
    # 3. 표시 이름에 @partner.example.com이 포함되지 않았는지 확인 (Exclude users with @partner.example.com in display name)
    if '@partner.example.com' in (user.fullname or '').lower():
        return False
    
    return True

def preview_test_users(server):
    """테스트 모드에서 변경될 처음 10명의 사용자 목록 미리보기 (Preview first 10 users to be updated in test mode)"""
    all_users = get_all_users(server)
    total_users = len(all_users)
    logging.info(f"총 {total_users}명의 사용자를 찾았습니다. (Total users found: {total_users})")
    
    users_to_update = [user for user in all_users if should_update_user(user)]
    total_matching = len(users_to_update)
    logging.info(f"조건에 맞는 사용자 수: {total_matching}명 (Users matching condition: {total_matching})")
    
    preview_users = users_to_update[:10]
    
    logging.info("\n=== 테스트 모드에서 변경될 처음 10명의 사용자 목록 (Preview of first 10 users to be updated in test mode) ===")
    for i, user in enumerate(preview_users, 1):
        logging.info(f"{i}. 사용자명: {user.name} (Username: {user.name})")
        logging.info(f"   표시이름: {user.fullname} (Display name: {user.fullname})")
        logging.info(f"   현재역할: {user.site_role} (Current role: {user.site_role})")
        logging.info(f"   마지막로그인: {user.last_login.strftime('%Y-%m-%d %H:%M:%S') if user.last_login else '없음'} (Last login: {user.last_login})")
        logging.info("---")
    
    filename = f'test_users_preview_{datetime.now().strftime("%Y%m%d_%H%M%S")}.csv'
    with open(filename, 'w', encoding='utf-8') as f:
        f.write("번호,사용자명,표시이름,현재역할,마지막로그인\n")  # (No., Username, Display Name, Current Role, Last Login)
        for i, user in enumerate(preview_users, 1):
            last_login = user.last_login.strftime("%Y-%m-%d %H:%M:%S") if user.last_login else "없음"
            f.write(f"{i},{user.name},{user.fullname},{user.site_role},{last_login}\n")
    
    logging.info(f"\n테스트 대상 사용자 목록이 {filename} 파일로도 저장되었습니다. (Preview user list saved to file: {filename})")
    logging.info("\n실제 역할 변경을 진행하려면 다음 명령어를 실행하세요: (Run the following command to apply role updates)")
    logging.info("python update_user_roles.py --update --role Explorer --test")

def check_users_to_update(server):
    """조건에 맞는 사용자 목록 추출 및 저장 (Extract and save users to be updated)"""
    all_users = get_all_users(server)
    total_users = len(all_users)
    logging.info(f"총 {total_users}명의 사용자를 찾았습니다. (Total users found: {total_users})")
    
    users_to_update = [user for user in all_users if should_update_user(user)]
    matching_users_count = len(users_to_update)
    logging.info(f"조건에 맞는 사용자 수: {matching_users_count}명 (Matching users: {matching_users_count})")
    
    filename = f'users_to_check_{datetime.now().strftime("%Y%m%d_%H%M%S")}.csv'
    with open(filename, 'w', encoding='utf-8') as f:
        f.write("사용자명,표시이름,현재역할,마지막로그인\n")  # (Username, Display Name, Current Role, Last Login)
        for user in users_to_update:
            last_login = user.last_login.strftime("%Y-%m-%d %H:%M:%S") if user.last_login else "없음"
            f.write(f"{user.name},{user.fullname},{user.site_role},{last_login}\n")
    
    logging.info(f"사용자 목록이 {filename} 파일로 저장되었습니다. (User list saved to file: {filename})")
    logging.info("\n테스트 모드로 처음 10명의 목록을 확인하려면 --preview 옵션을 사용하세요: (Use --preview option to check first 10 users)")
    logging.info("python update_user_roles.py --preview")

def update_user_roles(server, new_role, test_mode=False):
    """조건에 맞는 사용자의 역할 업데이트 (Update site role of matching users)"""
    all_users = get_all_users(server)
    total_users = len(all_users)
    logging.info(f"총 {total_users}명의 사용자를 찾았습니다. (Total users found: {total_users})")
    
    users_to_update = [user for user in all_users if should_update_user(user)]
    total_matching = len(users_to_update)
    
    if test_mode:
        users_to_update = users_to_update[:10]
        logging.info(f"테스트 모드: 조건에 맞는 전체 {total_matching}명 중 처음 10명만 변경합니다. (Test mode: updating first 10 of {total_matching} users)")
    
    logging.info(f"변경 대상 사용자 수: {len(users_to_update)}명 (Users to update: {len(users_to_update)})")
    
    filename = f'users_to_update_{datetime.now().strftime("%Y%m%d_%H%M%S")}.csv'
    with open(filename, 'w', encoding='utf-8') as f:
        f.write("사용자명,표시이름,현재역할,마지막로그인\n")  # (Username, Display Name, Current Role, Last Login)
        for user in users_to_update:
            last_login = user.last_login.strftime("%Y-%m-%d %H:%M:%S") if user.last_login else "없음"
            f.write(f"{user.name},{user.fullname},{user.site_role},{last_login}\n")
    
    logging.info(f"변경 대상 사용자 목록이 {filename} 파일로 저장되었습니다. (Updated user list saved to file: {filename})")
    
    success_count = 0
    error_count = 0
    
    for user in users_to_update:
        try:
            original_role = user.site_role
            user.site_role = new_role
            server.users.update(user)
            success_count += 1
            logging.info(f"사용자 업데이트 성공: {user.name} ({original_role} -> {new_role}) (Updated successfully)")
            logging.info(f"표시 이름: {user.fullname} (Display name: {user.fullname})")
        except Exception as e:
            error_count += 1
            logging.error(f"사용자 {user.name} 업데이트 실패: {str(e)} (Update failed)")

    logging.info(f"업데이트 완료: 성공 {success_count}건, 실패 {error_count}건 (Update summary: {success_count} success, {error_count} failed)")

def main():
    parser = argparse.ArgumentParser(description='태블로 서버 사용자 역할 일괄 변경 (Bulk update of Tableau user roles)')
    parser.add_argument('--update', action='store_true', help='실제 역할 변경 수행 여부 (Apply updates)')
    parser.add_argument('--role', help='새로운 역할 (예: Explorer, Viewer, Creator) (New role to assign)')
    parser.add_argument('--test', action='store_true', help='테스트 모드: 처음 10명만 변경 (Test mode: update only 10 users)')
    parser.add_argument('--preview', action='store_true', help='테스트 모드에서 변경될 10명의 목록 미리보기 (Preview first 10 users to be updated)')

    args = parser.parse_args()
    
    if args.update and not args.role:
        parser.error("역할 변경 시에는 --role 옵션이 필요합니다. (Missing --role option for update)")

    try:
        server = connect_to_server()
        if args.preview:
            preview_test_users(server)
        elif args.update:
            update_user_roles(server, args.role, args.test)
        else:
            check_users_to_update(server)
    finally:
        if server is not None:
            server.auth.sign_out()

if __name__ == '__main__':
    main()
```

```markdown
# Tableau 사용자 역할 일괄 변경 스크립트

이 스크립트는 Tableau 서버의 사용자 역할을 일괄적으로 변경하기 위한 도구입니다.

## 기능

- 특정 조건에 맞는 사용자의 역할을 일괄 변경
- 테스트 모드 지원 (10명 제한)
- 변경 대상 사용자 미리보기 기능
- CSV 파일로 결과 저장
- 상세한 로깅 지원

## 사전 요구사항

- Python 3.10 이상
- tableauserverclient 라이브러리
- 필요한 패키지 설치:
```bash
pip install -r requirements.txt
```

## 설정

1. `config.py` 파일에 서버 접속 정보를 설정합니다:
```python
config = {
    'my_env': {
        'server': '서버주소',
        'username': '사용자이름',
        'password': '비밀번호'
    }
}
```

## 사용 방법

### 1. 변경 대상 사용자 미리보기 (신규 기능)

테스트 모드에서 변경될 처음 10명의 사용자 목록을 미리 확인할 수 있습니다:
```bash
python update_user_roles.py --preview
```
- 로그에 10명의 상세 정보가 표시됩니다
- `test_users_preview_[날짜시간].csv` 파일로도 저장됩니다
- 미리보기를 통해 변경 대상을 확인한 후 실제 변경을 진행할 수 있습니다

### 2. 전체 대상 사용자 확인

모든 변경 대상 사용자 목록을 확인:
```bash
python update_user_roles.py
```

### 3. 테스트 모드로 역할 변경

처음 10명의 사용자만 역할 변경 (테스트 목적):
```bash
python update_user_roles.py --update --role Explorer --test
```

### 4. 전체 사용자 역할 변경

모든 대상 사용자의 역할 변경:
```bash
python update_user_roles.py --update --role Explorer
```

## 역할 종류

사용 가능한 역할:
- Viewer
- Explorer
- ExplorerCanPublish
- Creator

## 로그 확인

- 모든 작업 내역은 `role_check_[날짜시간].log` 파일에 저장됩니다
- 변경된 사용자 목록은 CSV 파일로도 저장됩니다

## 주의사항

1. 역할 변경 전에 반드시 미리보기 기능으로 대상자를 확인하세요
2. 테스트 모드로 먼저 실행해보는 것을 권장합니다
3. 변경 작업은 되돌릴 수 없으니 신중하게 진행하세요

## 문제 해결

오류가 발생하는 경우:
1. 로그 파일을 확인하세요
2. 서버 연결 정보가 올바른지 확인하세요
3. 필요한 라이브러리가 모두 설치되어 있는지 확인하세요 
```

```markdown
# Bulk Update Tableau User Roles Script

This script automates the process of updating user roles in a Tableau Server environment based on specific conditions.

## Features

- Bulk update of user roles that match specific conditions
- Test mode supported (limited to 10 users)
- Preview of users to be updated
- Results saved as CSV files
- Detailed logging provided

## Requirements

- Python 3.10 or higher
- tableauserverclient library
- Install dependencies:
```bash
pip install -r requirements.txt
```

## Configuration

1. Set up your Tableau Server connection info in config.py:
```python
config = {
    'my_env': {
        'server': 'https://your-server-url',
        'username': 'your-username',
        'password': 'your-password'
    }
}
```

## How to Use

### 1. Preview users to be updated (new feature)

You can preview the first 10 users that would be updated in test mode:
```bash
python update_user_roles.py --preview
```
- Detailed info of 10 users will be printed to the log
- A file named `test_users_preview_[datetime].csv` will be generated
- Review before running the actual update

### 2. List all users to be updated

Check the full list of users who meet the criteria:
```bash
python update_user_roles.py
```

### 3. Test mode update

Update only the first 10 users (for testing purposes):
```bash
python update_user_roles.py --update --role Explorer --test
```

### 4. Update all matching users

Run the actual role update for all matching users:
```bash
python update_user_roles.py --update --role Explorer
```

## Available Roles

- Viewer
- Explorer
- ExplorerCanPublish
- Creator

## Log Output

- All operations are logged in `role_check_[datetime].log`
- Updated user lists are saved as CSV files

## Notes

1. Always use the preview option to review affected users before applying changes
2. It’s highly recommended to run in test mode first
3. Role changes cannot be undone, proceed with caution

## Troubleshooting

If you encounter errors:
1. Check the log file for details
2. Verify your Tableau Server credentials and configuration
3. Ensure all required libraries are installed
```
