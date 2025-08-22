<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>한국 뮤지컬 정보 검색</title>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700&display=swap" rel="stylesheet">
    <style>
        /* CSS 코드 시작 */
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #f4f7f9;
            color: #333;
            margin: 0;
            padding: 0;
            line-height: 1.6;
        }

        header {
            background-color: #fff;
            padding: 20px;
            text-align: center;
            border-bottom: 2px solid #ddd;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        h1 {
            color: #4CAF50;
            margin-bottom: 15px;
        }

        .search-container {
            display: flex;
            justify-content: center;
        }

        #searchInput {
            padding: 10px 15px;
            border: 2px solid #ddd;
            border-radius: 25px;
            width: 60%;
            max-width: 400px;
            outline: none;
            transition: all 0.3s ease;
        }

        #searchInput:focus {
            border-color: #4CAF50;
            box-shadow: 0 0 8px rgba(76, 175, 80, 0.3);
        }

        #searchButton {
            padding: 10px 20px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 25px;
            margin-left: 10px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        #searchButton:hover {
            background-color: #45a049;
        }

        main {
            padding: 20px;
            max-width: 1200px;
            margin: auto;
        }

        #resultsContainer {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .musical-card {
            background-color: #fff;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
            overflow: hidden;
            cursor: pointer;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .musical-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 6px 15px rgba(0,0,0,0.15);
        }

        .card-image {
            width: 100%;
            height: 300px;
            object-fit: cover;
        }

        .card-content {
            padding: 15px;
        }

        .card-title {
            font-size: 1.2em;
            font-weight: bold;
            color: #333;
            margin-bottom: 5px;
        }

        .card-info {
            font-size: 0.9em;
            color: #666;
        }

        /* 모달 스타일 */
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.6);
            overflow: auto;
            padding-top: 60px;
        }

        .modal-content {
            background-color: #fff;
            margin: 5% auto;
            padding: 30px;
            border-radius: 10px;
            width: 90%;
            max-width: 700px;
            position: relative;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            animation: fadeIn 0.5s;
        }

        @keyframes fadeIn {
            from {opacity: 0;}
            to {opacity: 1;}
        }

        .close-button {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
            position: absolute;
            top: 10px;
            right: 20px;
            cursor: pointer;
        }

        .close-button:hover,
        .close-button:focus {
            color: #333;
            text-decoration: none;
        }

        #modal-body h2 {
            color: #4CAF50;
            border-bottom: 2px solid #eee;
            padding-bottom: 10px;
            margin-top: 0;
        }

        #modal-body p {
            margin-bottom: 10px;
        }

        #modal-body .info-section {
            margin-bottom: 20px;
        }

        #modal-body ul {
            list-style-type: none;
            padding: 0;
        }

        #modal-body ul li {
            background-color: #f9f9f9;
            border-left: 4px solid #4CAF50;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 5px;
        }

        .review-section {
            margin-top: 20px;
            border-top: 1px dashed #ddd;
            padding-top: 20px;
        }

        .review {
            background-color: #f0f4f7;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
        }

        .review-author {
            font-weight: bold;
            color: #555;
            margin-bottom: 5px;
        }

        .review-text {
            font-style: italic;
            color: #777;
        }

        .ticket-link {
            display: inline-block;
            padding: 12px 25px;
            background-color: #FF5722;
            color: white;
            text-decoration: none;
            border-radius: 25px;
            font-weight: bold;
            margin-top: 20px;
            transition: background-color 0.3s ease;
        }

        .ticket-link:hover {
            background-color: #e64a19;
        }
        /* CSS 코드 끝 */
    </style>
</head>
<body>

    <header>
        <h1>한국 뮤지컬 정보 검색</h1>
        <div class="search-container">
            <input type="text" id="searchInput" placeholder="뮤지컬 제목을 입력하세요...">
            <button id="searchButton">검색</button>
        </div>
    </header>

    <main>
        <div id="resultsContainer">
            </div>
    </main>

    <div id="modal" class="modal">
        <div class="modal-content">
            <span class="close-button">&times;</span>
            <div id="modal-body">
                </div>
        </div>
    </div>

    <script>
        // JavaScript 코드 시작
        // 검색 버튼과 입력창 DOM 요소 가져오기
        const searchInput = document.getElementById('searchInput');
        const searchButton = document.getElementById('searchButton');
        const resultsContainer = document.getElementById('resultsContainer');
        const modal = document.getElementById('modal');
        const modalBody = document.getElementById('modal-body');
        const closeButton = document.querySelector('.close-button');

        // 임시 뮤지컬 데이터 (실제로는 API를 통해 가져와야 함)
        const dummyMusicals = [
            {
                id: 1,
                title: '레 미제라블',
                image: 'https://cdn.pixabay.com/photo/2023/12/28/19/31/musical-8474921_1280.jpg', // 임시 이미지
                dates: '2025.10.15 - 2026.01.20',
                location: '블루스퀘어 신한카드홀',
                story: '19세기 프랑스를 배경으로 전과자 장발장의 파란만장한 삶을 그린 대서사시.',
                showtimes: [
                    { day: '월요일', time: '19:30 - 22:30' },
                    { day: '화요일', time: '공연 없음' },
                    { day: '수요일', time: '14:00 - 17:00, 19:30 - 22:30' },
                    { day: '목요일', time: '19:30 - 22:30' },
                    { day: '금요일', time: '19:30 - 22:30' },
                    { day: '토요일', time: '14:00 - 17:00, 19:30 - 22:30' },
                    { day: '일요일', time: '15:00 - 18:00' }
                ],
                reviews: [
                    { author: '김하나', text: '웅장한 음악과 배우들의 열연에 감동받았습니다. 정말 명작이네요!' },
                    { author: '이철수', text: '스토리가 탄탄하고 배우들의 가창력이 돋보였습니다. 꼭 다시 보고 싶어요.' }
                ],
                ticketUrl: 'https://www.example-ticket-site.com/lesmis' // 임시 티켓 사이트
            },
            {
                id: 2,
                title: '오페라의 유령',
                image: 'https://cdn.pixabay.com/photo/2016/11/29/10/44/theatre-1868351_1280.jpg', // 임시 이미지
                dates: '2025.11.01 - 2026.02.28',
                location: '샤롯데씨어터',
                story: '파리 오페라 극장 지하에 숨어 사는 팬텀과 프리마돈나 크리스틴의 비극적인 사랑 이야기.',
                showtimes: [
                    { day: '월요일', time: '공연 없음' },
                    { day: '화요일', time: '19:00 - 22:00' },
                    { day: '수요일', time: '19:00 - 22:00' },
                    { day: '목요일', time: '14:00 - 17:00, 19:00 - 22:00' },
                    { day: '금요일', time: '19:00 - 22:00' },
                    { day: '토요일', time: '14:00 - 17:00, 19:00 - 22:00' },
                    { day: '일요일', time: '15:00 - 18:00' }
                ],
                reviews: [
                    { author: '박서준', text: '아름다운 음악과 화려한 무대 연출이 인상 깊었습니다. 팬텀의 감정 연기가 최고였어요.' },
                    { author: '최지우', text: '크리스틴의 목소리에 소름이 돋았습니다. 환상적인 공연이었어요.' }
                ],
                ticketUrl: 'https://www.example-ticket-site.com/phantom' // 임시 티켓 사이트
            }
        ];

        // 검색 기능 함수
        function performSearch() {
            const searchTerm = searchInput.value.toLowerCase();
            const filteredMusicals = dummyMusicals.filter(musical => 
                musical.title.toLowerCase().includes(searchTerm)
            );
            displayResults(filteredMusicals);
        }

        // 검색 결과 표시 함수
        function displayResults(musicals) {
            resultsContainer.innerHTML = ''; // 기존 결과 지우기
            if (musicals.length === 0) {
                resultsContainer.innerHTML = '<p>😭 검색 결과가 없습니다.</p>';
                return;
            }
            musicals.forEach(musical => {
                const card = document.createElement('div');
                card.classList.add('musical-card');
                card.innerHTML = `
                    <img src="${musical.image}" alt="${musical.title}" class="card-image">
                    <div class="card-content">
                        <h3 class="card-title">${musical.title}</h3>
                        <p class="card-info">📅 ${musical.dates}</p>
                        <p class="card-info">📍 ${musical.location}</p>
                    </div>
                `;
                card.addEventListener('click', () => showMusicalDetails(musical));
                resultsContainer.appendChild(card);
            });
        }

        // 뮤지컬 상세 정보 모달 표시 함수
        function showMusicalDetails(musical) {
            let showtimesHtml = musical.showtimes.map(st => 
                `<li><strong>${st.day}:</strong> ${st.time}</li>`
            ).join('');

            let reviewsHtml = musical.reviews.map(review => 
                `<div class="review">
                    <p class="review-author"><strong>${review.author}</strong></p>
                    <p class="review-text">"${review.text}"</p>
                </div>`
            ).join('');

            modalBody.innerHTML = `
                <h2>${musical.title}</h2>
                <div class="info-section">
                    <h3>📖 줄거리</h3>
                    <p>${musical.story}</p>
                </div>
                <div class="info-section">
                    <h3>⏰ 공연 시간</h3>
                    <ul>
                        ${showtimesHtml}
                    </ul>
                </div>
                <div class="info-section review-section">
                    <h3>📝 사람들의 리뷰</h3>
                    ${reviewsHtml || '<p>아직 리뷰가 없습니다.</p>'}
                </div>
                <a href="${musical.ticketUrl}" target="_blank" class="ticket-link">🎟️ 티켓 예매하기</a>
            `;
            modal.style.display = 'block';
        }

        // 모달 닫기 기능
        closeButton.addEventListener('click', () => {
            modal.style.display = 'none';
        });

        // 모달 바깥 클릭 시 닫기
        window.addEventListener('click', (event) => {
            if (event.target == modal) {
                modal.style.display = 'none';
            }
        });

        // 검색 버튼 클릭 이벤트
        searchButton.addEventListener('click', performSearch);

        // Enter 키로 검색 실행
        searchInput.addEventListener('keyup', (event) => {
            if (event.key === 'Enter') {
                performSearch();
            }
        });

        // 페이지 로드 시 전체 목록 표시
        window.onload = () => {
            displayResults(dummyMusicals);
        };
        // JavaScript 코드 끝
    </script>
</body>
</html>