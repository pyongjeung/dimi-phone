import subprocess
import sys


def install(package):
    subprocess.check_call([sys.executable, "-m", "pip", "install", package])


for pkg in ["pygame", "opencv-python", "numpy"]:
    try:
        __import__(pkg if pkg != "opencv-python" else "cv2")
    except ImportError:
        install(pkg)

import pygame
import datetime
import cv2
import numpy as np


class Phone:
    def __init__(self):
        self.width = 400
        self.height = 700
        self.today = datetime.datetime.now()
        self.weeklist = ['월', '화', '수', '목', '금', '토', '일']
        self.week = self.weeklist[self.today.weekday()]

        pygame.init()
        pygame.mixer.init()
        self.shutter_sound = pygame.mixer.Sound("camera-shutter.mp3")

        self.screen = pygame.display.set_mode((self.width, self.height))
        pygame.display.set_caption("Dimi-Phone")
        self.clock = pygame.time.Clock()
        self.running = True
        self.state = "lock"  # 현재 화면 상태

        self.font = pygame.font.SysFont("malgungothic", 24, True)
        self.sub_f = pygame.font.SysFont("malgungothic", 15, True)

        # 이미지 로드 및 크기 조정
        self.background = pygame.image.load("wallpaper.jpg").convert()
        self.background = pygame.transform.smoothscale(self.background, (self.width, self.height))
        self.phone = pygame.image.load("phone.png").convert_alpha()
        self.phone = pygame.transform.smoothscale(self.phone, (self.width, self.height))

    def unlocked_phone(self):
        import datetime
        today = datetime.datetime.now()
        weeklist = ['월', '화', '수', '목', '금', '토', '일']
        week = weeklist[today.weekday()]
        p_date = f"{today.month}월 {today.day}일 {week}요일"

        # 배경
        self.screen.blit(self.background, (0, 0))
        self.screen.blit(self.phone, (0, 0))

        # 날짜
        text = self.sub_f.render(p_date, False, (255, 255, 255))
        self.screen.blit(text, [146, self.height // 9])

        # 시간
        font = pygame.font.SysFont("malgungothic", 50, True)
        text = font.render(f"{today.hour}:{today.minute:02d}", False, (255, 255, 255))
        self.screen.blit(text, [135, self.height // 7])

        # 안내 문구
        text = self.sub_f.render("'space'를 통해 잠금해제", False, (213, 222, 232))
        self.screen.blit(text, [120, self.height // 6 * 5])
        pygame.display.flip()

        # 클릭 시 홈화면으로 전환
        if pygame.key.get_pressed()[pygame.K_SPACE]:
            self.state = "home"

    def opened_phone(self):
        # 홈화면 한 번만 그림
        self.screen.fill((0, 0, 0))
        self.screen.blit(self.background, (0, 0))
        self.screen.blit(self.phone, (0, 0))

        # 상단 시계
        font = pygame.font.SysFont("malgungothic", 13, True)
        text = font.render(f"{self.today.hour} : {self.today.minute:02d}", False, (0, 0, 0))
        self.screen.blit(text, [65, self.height // 29])

        # 허전한 부분 채우기
        pygame.draw.rect(self.screen, (79, 164, 204), (65, 80, 125, 125), border_radius=20)
        pygame.draw.rect(self.screen, (79, 164, 204), (210, 80, 125, 125), border_radius=20)  ######나중에 최신 사진으로 업데이트

        # 앱 아이콘 만들기
        # 카메라--------------------------------------------
        camera_icon = pygame.image.load("camera.png").convert()
        camera_icon = pygame.transform.smoothscale(camera_icon, (60, 60))
        self.screen.blit(camera_icon, (85, 250))

        # 카메라 앱 실행
        if 85 <= pygame.mouse.get_pos()[0] <= 145 and 250 <= pygame.mouse.get_pos()[1] <= 310:
            if pygame.mouse.get_pressed()[0]:
                self.state = "camera"
        # --------------------------------------------------

        # 사진앱--------------------------------------------
        photo_icon = pygame.image.load("photo.png").convert()
        photo_icon = pygame.transform.smoothscale(photo_icon, (60, 60))
        self.screen.blit(photo_icon, (220, 250))

        # 사진 앱 실행
        if 220 <= pygame.mouse.get_pos()[0] <= 280 and 250 <= pygame.mouse.get_pos()[1] <= 310:
            if pygame.mouse.get_pressed()[0]:
                self.state = "photo"
        # --------------------------------------------------

        # 캘린더--------------------------------------------
        calendar_icon = pygame.image.load("calendar.png").convert()
        calendar_icon = pygame.transform.smoothscale(calendar_icon, (60, 60))
        self.screen.blit(calendar_icon, (85, 400))

        # 캘린더 앱 실행
        if 85 <= pygame.mouse.get_pos()[0] <= 145 and 400 <= pygame.mouse.get_pos()[1] <= 460:
            if pygame.mouse.get_pressed()[0]:
                self.state = "calendar"
        # --------------------------------------------------

        # 계산기--------------------------------------------
        calculator_icon = pygame.image.load("calculator.png").convert()
        calculator_icon = pygame.transform.smoothscale(calculator_icon, (60, 60))
        self.screen.blit(calculator_icon, (220, 400))

        # 계산기 앱 실행
        if 220 <= pygame.mouse.get_pos()[0] <= 280 and 400 <= pygame.mouse.get_pos()[1] <= 460:
            if pygame.mouse.get_pressed()[0]:
                self.state = "calculator"
        # --------------------------------------------------

        # 미니게임--------------------------------------------
        game_icon = pygame.image.load("game.png").convert()
        game_icon = pygame.transform.smoothscale(game_icon, (60, 60))
        self.screen.blit(game_icon, (85, 550))

        # 미니게임 실행
        if 85 <= pygame.mouse.get_pos()[0] <= 145 and 550 <= pygame.mouse.get_pos()[1] <= 610:
            if pygame.mouse.get_pressed()[0]:
                self.state = "game"
        # ----------------------------------------------------

        pygame.display.flip()
        # 잠금화면으로 돌아가기
        if pygame.key.get_pressed()[pygame.K_DELETE]:
            self.state = "lock"

    def camera_app(self):
        import datetime, os
        cap = cv2.VideoCapture(0, cv2.CAP_DSHOW)

        running_camera = True
        photo_cooldown = 0  # 촬영 딜레이

        while running_camera:
            for e in pygame.event.get():
                if e.type == pygame.QUIT:
                    running_camera = False
                    self.running = False
                elif e.type == pygame.KEYDOWN:
                    running_camera = False
                    if e.key == pygame.K_BACKSPACE:
                        self.state = "home"
                        pygame.event.clear()

            ret, frame = cap.read()
            if not ret:
                break

            # OpenCV --> pygame 변환
            frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
            frame = np.rot90(frame)
            frame_surface = pygame.surfarray.make_surface(frame)
            frame_surface = pygame.transform.scale(frame_surface, (self.width, self.height))
            self.screen.blit(frame_surface, (0, 0))

            # UI 그리기
            pygame.draw.rect(self.screen, (0, 0, 0), (0, 0, self.width, 80))  # 상단 바
            pygame.draw.circle(self.screen, (0, 255, 0), (self.width // 2, 20), 5)  # 초록 점
            pygame.draw.rect(self.screen, (0, 0, 0), (0, self.height - 120, self.width, 120))  # 하단 바
            pygame.draw.circle(self.screen, (255, 255, 255),
                               (self.width // 2, self.height - 60), 30, width=4)
            text = self.sub_f.render("사진", True, (255, 255, 0))
            self.screen.blit(text, (self.width // 2 - 17, self.height - 110))

            # 사진 촬영 (촬영 버튼)
            mx, my = pygame.mouse.get_pos()
            if (self.width // 2 - 30) <= mx <= (self.width // 2 + 30) and \
                    (self.height - 90) <= my <= (self.height - 30):

                if pygame.mouse.get_pressed()[0] and photo_cooldown == 0:
                    photo_cooldown = 10  # 10프레임(0.3초 정도) 대기

                    # 깜빡임 + 소리
                    self.screen.fill((255, 255, 255))
                    pygame.display.flip()
                    self.shutter_sound.stop()
                    self.shutter_sound.play()

                    # 사진 저장
                    save_dir = "photos"
                    os.makedirs(save_dir, exist_ok=True)
                    now = datetime.datetime.now()
                    photo_filename = now.strftime("photo_%Y%m%d_%H%M%S.jpg")
                    save_path = os.path.join(save_dir, photo_filename)
                    # 현재 프레임을 BGR 형식으로 변환 후 저장
                    frame_bgr = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)

                    # pygame에서 세로로 돌린 상태니까 다시 -90도 회전시켜서 원래 방향으로 복구
                    frame_bgr = np.rot90(frame_bgr, 3)

                    # 해상도 맞추기
                    frame_bgr = cv2.resize(frame_bgr, (self.width, self.height), interpolation=cv2.INTER_AREA)

                    cv2.imwrite(save_path, frame_bgr)

            # 쿨다운 카운터 감소
            if photo_cooldown > 0:
                photo_cooldown -= 1

            pygame.display.flip()
            self.clock.tick(30)

        cap.release()

    def photo_app(self):
        import os, pygame

        thumb_size = 65  # 각 썸네일 크기
        per_row = 4  # 가로 4장
        per_col = 7  # 세로 7행
        gap = 10  # 사진 간 간격
        load_limit = per_row * per_col  # 한 페이지당 28장
        top_padding = 150

        # 사진 불러오기
        photos = []
        if os.path.exists("photos"):
            for f in sorted(os.listdir("photos"), reverse=True):
                if f.lower().endswith((".jpg")):
                    photos.append(os.path.join("photos", f))

        total_pages = max(1, (len(photos) + load_limit - 1) // load_limit)
        if not hasattr(self, "photo_page"):
            self.photo_page = 0

        start = self.photo_page * load_limit
        end = start + load_limit
        show_list = photos[start:end]

        # 화면 초기화
        self.screen.fill((255, 255, 255))
        self.screen.blit(self.phone, (0, 0))

        # 상단 시계
        font_small = pygame.font.SysFont("malgungothic", 13, True)
        t = font_small.render(f"{self.today.hour} : {self.today.minute:02d}", False, (0, 0, 0))
        self.screen.blit(t, [65, self.height // 29])

        # 제목
        font_title = pygame.font.SysFont("malgungothic", 35, True)
        title = font_title.render("보관함", False, (0, 0, 0))
        self.screen.blit(title, [self.width // 8, self.height // 17])

        # 썸네일 표시
        x_start = (self.width - ((thumb_size * per_row) + gap * (per_row - 1))) // 2
        x, y = x_start, top_padding

        for i, path in enumerate(show_list):
            try:
                img = pygame.image.load(path).convert()
                img = pygame.transform.smoothscale(img, (thumb_size, thumb_size))
                self.screen.blit(img, (x, y))
            except:
                pass

            x += thumb_size + gap
            if (i + 1) % per_row == 0:
                x = x_start
                y += thumb_size + gap

        # 페이지 이동 버튼
        font_btn = pygame.font.SysFont("malgungothic", 20, True)
        btn_prev, btn_next = None, None

        if self.photo_page > 0:
            btn_prev = pygame.Rect(self.width // 2 - 130, self.height - 65, 120, 40)
            pygame.draw.rect(self.screen, (79, 164, 204), btn_prev, border_radius=15)
            self.screen.blit(font_btn.render("이전", True, (255, 255, 255)),
                             (btn_prev.x + 28, btn_prev.y + 8))

        if self.photo_page < total_pages - 1:
            btn_next = pygame.Rect(self.width // 2 + 10, self.height - 65, 120, 40)
            pygame.draw.rect(self.screen, (79, 164, 204), btn_next, border_radius=15)
            self.screen.blit(font_btn.render("다음", True, (255, 255, 255)),
                             (btn_next.x + 20, btn_next.y + 8))

        pygame.display.flip()

        # 이벤트 처리
        for e in pygame.event.get():
            if e.type == pygame.QUIT:
                self.running = False
            elif e.type == pygame.KEYDOWN:
                if e.key == pygame.K_BACKSPACE:
                    self.state = "home"
                    pygame.event.clear()
            elif e.type == pygame.MOUSEBUTTONDOWN and e.button == 1:
                if btn_next and btn_next.collidepoint(e.pos):
                    self.photo_page += 1
                    self.photo_app()
                elif btn_prev and btn_prev.collidepoint(e.pos):
                    self.photo_page -= 1
                    self.photo_app()

    def calendar_app(self):
        import calendar, datetime

        # 현재 날짜
        if not hasattr(self, "cal_now"):
            self.cal_now = datetime.datetime.now()

        # 화면 초기화
        self.screen.fill((255, 255, 255))
        self.screen.blit(self.phone, (0, 0))

        # 상단 시계
        font = pygame.font.SysFont("malgungothic", 15, True)
        self.screen.blit(font.render(self.cal_now.strftime("%H:%M"), True, (0, 0, 0)), (65, 20))

        # 달력 표시
        cal = calendar.TextCalendar(firstweekday=6)
        text = cal.formatmonth(self.cal_now.year, self.cal_now.month)
        cal_font = pygame.font.SysFont("consolas", 26)
        for i, line in enumerate(text.splitlines()):
            self.screen.blit(cal_font.render(line, True, (0, 0, 0)), (60, 170 + i * 40))

        pygame.display.flip()

        # 이벤트 처리
        for e in pygame.event.get():
            if e.type == pygame.QUIT:
                self.running = False
            elif e.type == pygame.KEYDOWN:
                if e.key == pygame.K_BACKSPACE:
                    self.state = "home"
                elif e.key == pygame.K_LEFT:
                    if self.cal_now.month == 1:
                        self.cal_now = self.cal_now.replace(year=self.cal_now.year - 1, month=12)
                    else:
                        self.cal_now = self.cal_now.replace(month=self.cal_now.month - 1)
                elif e.key == pygame.K_RIGHT:
                    if self.cal_now.month == 12:
                        self.cal_now = self.cal_now.replace(year=self.cal_now.year + 1, month=1)
                    else:
                        self.cal_now = self.cal_now.replace(month=self.cal_now.month + 1)

    def calculator_app(self):
        font = pygame.font.SysFont("malgungothic", 25, True)
        big = pygame.font.SysFont("malgungothic", 30, True)

        # 상태값
        self.calc_input = getattr(self, "calc_input", "")
        self.calc_result = getattr(self, "calc_result", "")

        # 버튼 구조
        buttons = [['7', '8', '9', '/'], ['4', '5', '6', '*'],
                   ['1', '2', '3', '-'], ['0', 'C', '=', '+']]
        bw, bh = 55, 40
        sx, sy = 60, 250

        def draw():
            s = self.screen
            s.fill((255, 255, 255))
            s.blit(self.phone, (0, 0))
            # 입력, 결과
            pygame.draw.rect(s, (230, 230, 230), (60, 150, 280, 60))
            s.blit(big.render(self.calc_input, True, (0, 0, 0)), (70, 160))
            s.blit(big.render(self.calc_result, True, (0, 0, 255)), (70, 210))
            # 버튼
            for i, row in enumerate(buttons):
                for j, v in enumerate(row):
                    r = pygame.Rect(sx + j * (bw + 20), sy + i * (bh + 20), bw, bh)
                    pygame.draw.rect(s, (220, 220, 220), r)
                    pygame.draw.rect(s, (0, 0, 0), r, 2)
                    s.blit(font.render(v, True, (0, 0, 0)), (r.x + 20, r.y + 5))
            pygame.display.update()

        draw()

        while self.running:
            for e in pygame.event.get():
                if e.type == pygame.QUIT:
                    self.running = False
                    return
                elif e.type == pygame.KEYDOWN:
                    if e.key == pygame.K_BACKSPACE:
                        self.state = "home"
                        return

                if e.type == pygame.MOUSEBUTTONDOWN and e.button == 1:  # 버튼 클릭(계산기)
                    x, y = e.pos
                    for i, row in enumerate(buttons):
                        for j, v in enumerate(row):
                            r = pygame.Rect(sx + j * (bw + 20), sy + i * (bh + 20), bw, bh)
                            if r.collidepoint(x, y):
                                if v == 'C':
                                    self.calc_input, self.calc_result = "", ""
                                elif v == '=':
                                    try:
                                        self.calc_result = str(eval(self.calc_input))
                                    except:
                                        self.calc_result = "Error"
                                else:
                                    self.calc_input += v
                                draw();
                                break

    def mini_game(self):
        import random
        w, h = 400, 700
        player = pygame.Rect(180, 600, 20, 20)
        enemies = []
        speed = 6
        a = 1
        score = 0
        clock = pygame.time.Clock()
        running = True

        while running:
            self.screen.fill((255, 255, 255))
            self.screen.blit(self.phone, (0, 0))
            pygame.draw.rect(self.screen, (0, 150, 255), player)
            for e in enemies:
                pygame.draw.rect(self.screen, (255, 0, 0), e)
                e.y += speed
            enemies = [e for e in enemies if e.y < h]

            score += a
            # 점수 표시
            font = pygame.font.SysFont("malgungothic", 20, True)
            score_text = font.render(f"점수: {score}", True, (0, 0, 0))
            self.screen.blit(score_text, (60, 50))
            # 적 스폰
            if random.randint(1, 30) == 2:
                enemies.append(pygame.Rect(random.randint(45, w - 200), -40, 30, 30))
                enemies.append(pygame.Rect(random.randint(w - 200, w - 78), -40, 30, 30))

            # 충돌 판정
            for e in enemies:
                if player.colliderect(e):
                    self.screen.fill((255, 255, 255))
                    self.screen.blit(self.phone, (0, 0))
                    # 점수 표시
                    font = pygame.font.SysFont("malgungothic", 50, True)
                    score_text = font.render(f"점수: {score}", True, (0, 0, 0))
                    self.screen.blit(score_text, (90, 200))
                    pygame.display.update()
                    import time
                    time.sleep(3)
                    running = False

            # 입력 처리
            for e in pygame.event.get():
                if e.type == pygame.QUIT:
                    self.running = False
                    return
                elif e.type == pygame.KEYDOWN and e.key == pygame.K_BACKSPACE:
                    self.state = "home"
                    pygame.event.clear()
                    return

            keys = pygame.key.get_pressed()
            if keys[pygame.K_LEFT] and player.x > 50:
                player.x -= 6
            if keys[pygame.K_RIGHT] and player.x < w - 65:
                player.x += 6
            if keys[pygame.K_UP]:
                speed += 1
                a += 1
            if keys[pygame.K_DOWN] and speed > 1:
                speed -= 1
                a += -1

            pygame.display.update()
            clock.tick(60)

    def run(self):
        while self.running:
            for e in pygame.event.get():
                if e.type == pygame.QUIT:
                    self.running = False
            if pygame.key.get_pressed()[pygame.K_ESCAPE]:
                self.running = False

            # 상태에 따라 다른 화면
            if self.state == "lock":
                self.unlocked_phone()
            elif self.state == "home":
                self.opened_phone()
            elif self.state == "camera":
                self.camera_app()
            elif self.state == "photo":
                self.photo_app()
            elif self.state == "calendar":
                self.calendar_app()
            elif self.state == "calculator":
                self.calculator_app()
            elif self.state == "game":
                self.mini_game()
            self.clock.tick(60)

        pygame.quit()


if __name__ == "__main__":
    Phone().run()
