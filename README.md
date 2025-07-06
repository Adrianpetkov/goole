from flask import Flask, request, send_from_directory, render_template_string
import os

app = Flask(__name__)
UPLOAD_FOLDER = 'videos'
os.makedirs(UPLOAD_FOLDER, exist_ok=True)

@app.route('/')
def index():
    video_files = os.listdir(UPLOAD_FOLDER)
    video_links = ''.join(f'<li><a href="/videos/{v}">{v}</a></li>' for v in video_files)

    html_content = f"""
    <h1>Качи видео към FlightReality</h1>
    <form action="/upload" method="POST" enctype="multipart/form-data">
      <input type="file" name="video" accept="video/*" required>
      <button type="submit">Качи</button>
    </form>
    <h2>📂 Качени видеа:</h2>
    <ul>
      {video_links}
    </ul>
    """
    return render_template_string(html_content)

@app.route('/upload', methods=['POST'])
def upload():
    video = request.files.get('video')
    if video:
        video.save(os.path.join(UPLOAD_FOLDER, video.filename))
        return f"✅ Успешно качено: {video.filename}<br><br><a href='/'>⬅ Обратно</a>"
    return "❌ Няма избран файл"

@app.route('/videos/<filename>')
def serve_video(filename):
    return send_from_directory(UPLOAD_FOLDER, filename)

if __name__ == '__main__':
    app.run(debug=True)
