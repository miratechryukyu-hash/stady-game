import streamlit as st
import pandas as pd

# ページ設定
st.set_page_config(
    page_title="SmartManual AI",
    layout="wide"
)

# セッション状態の初期化
if "chat_history" not in st.session_state:
    st.session_state.chat_history = [
        {"role": "assistant", "content": "こんにちは。本日の業務で迷っていることはありませんか？何でも聞いてください。"}
    ]
if "view_counts" not in st.session_state:
    st.session_state.view_counts = {
        "機器の起動手順": 12,
        "トラブルシューティング（エラーE-01）": 8,
        "清掃・メンテナンス方法": 3,
        "安全確認チェックリスト": 1
    }

# サイドバー：ナビゲーション
st.sidebar.title("SmartManual AI")
menu = st.sidebar.selectbox(
    "メニューを選択",
    ["AIガイド＆検索", "マニュアル閲覧", "学習分析", "理解度チェック"]
)

# 1. AIガイド＆検索
if menu == "AIガイド＆検索":
    st.title("AI対話型マニュアルナビゲーター")
    st.markdown("スライドを探す手間を省き、知りたい情報を瞬時に見つけます。")

    for chat in st.session_state.chat_history:
        if chat["role"] == "user":
            st.chat_message("user").write(chat["content"])
        else:
            st.chat_message("assistant").write(chat["content"])

    user_query = st.chat_input("例: エラーE-01の対処法を教えて")
    if user_query:
        st.session_state.chat_history.append({"role": "user", "content": user_query})
        
        if "エラー" in user_query or "E-01" in user_query:
            reply = "トラブルシューティング（エラーE-01）の項目です。スライド3ページ目、および解説動画を用意しています。"
            st.session_state.view_counts["トラブルシューティング（エラーE-01）"] += 1
        elif "起動" in user_query:
            reply = "機器の起動手順です。動画とスライドをリンクしました。"
            st.session_state.view_counts["機器の起動手順"] += 1
        else:
            reply = f"{user_query} に関連するマニュアルを横断検索しました。"

        st.session_state.chat_history.append({"role": "assistant", "content": reply})
        st.rerun()

# 2. マニュアル閲覧
elif menu == "マニュアル閲覧":
    st.title("マルチモーダル・マニュアルビューア")
    
    manual_category = st.selectbox(
        "マニュアルを選択してください",
        list(st.session_state.view_counts.keys())
    )
    
    st.session_state.view_counts[manual_category] += 1
    st.info(f"現在表示中: {manual_category}")

    tab_video, tab_slide, tab_text = st.tabs(["動画で見る", "スライド", "テキスト詳細"])

    with tab_video:
        st.subheader("解説動画")
        st.video("https://www.w3schools.com/html/mov_bbb.mp4")

    with tab_slide:
        st.subheader("スライド資料")
        st.write("スライド 1 / 5 ページ")

    with tab_text:
        st.subheader("テキスト・手順詳細")
        st.markdown("""
        1. 電源ケーブルがしっかりと挿入されていることを確認します。
        2. メインスイッチをONにします。
        3. 起動音が鳴り、ランプが緑色に点灯したら完了です。
        """)

# 3. 学習分析
elif menu == "学習分析":
    st.title("学習アナリティクス")
    st.markdown("閲覧頻度や苦手なポイントを数値化し、効率的な復習をサポートします。")

    df = pd.DataFrame(list(st.session_state.view_counts.items()), columns=["マニュアル項目", "閲覧回数"])
    
    col1, col2 = st.columns(2)
    with col1:
        st.subheader("アクセスランキング")
        st.dataframe(df.sort_values(by="閲覧回数", ascending=False), use_container_width=True)

    with col2:
        st.subheader("閲覧傾向グラフ")
        st.bar_chart(df.set_index("マニュアル項目"))

    st.warning("AIからのアドバイス: トラブルシューティングの閲覧回数が多くなっています。定期的なチェックをおすすめします。")

# 4. 理解度チェック
elif menu == "理解度チェック":
    st.title("理解度チェック")
    st.markdown("問題形式のアウトプットにより記憶の定着とモチベーション維持を図ります。")

    question_bank = [
        {
            "q": "エラーE-01が発生した際、最初に確認すべき箇所はどれか。",
            "options": ["メインスイッチの切断", "ケーブルの接続状況とエラーコードの再確認", "本体の分解清掃"],
            "answer": 1
        },
        {
            "q": "機器の起動時にランプが何色に点灯すれば正常か。",
            "options": ["赤色", "黄色", "緑色"],
            "answer": 2
        }
    ]

    if "quiz_index" not in st.session_state:
        st.session_state.quiz_index = 0

    q_data = question_bank[st.session_state.quiz_index % len(question_bank)]

    st.subheader(f"第 {st.session_state.quiz_index + 1} 問")
    st.write(q_data["q"])

    user_choice = st.radio("選択肢を選んでください", q_data["options"], key=f"q_{st.session_state.quiz_index}")

    if st.button("回答する"):
        selected_idx = q_data["options"].index(user_choice)
        if selected_idx == q_data["answer"]:
            st.success("正解です。素晴らしい理解度です。")
        else:
            st.error("不正解です。該当マニュアルの復習をおすすめします。")

    if st.button("次の問題へ"):
        st.session_state.quiz_index += 1
        st.rerun()
