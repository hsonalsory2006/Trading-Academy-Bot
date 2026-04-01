import logging
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes

# --- الإعدادات الخاصة بك ---
TOKEN = '7548201559:AAGaQHnvbgGLlZJlJWzPWRSvlapn9hq-c9o'
ADMIN_ID = '5408074424'
CHANNEL_URL = 'https://t.me/N3_T01'

# إعداد السجلات لمراقبة أداء البوت
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    # إنشاء أزرار تفاعلية
    keyboard = [
        [InlineKeyboardButton("📢 تابع قناتنا على تلجرام", url=CHANNEL_URL)],
        [InlineKeyboardButton("📚 دورات الأكاديمية (SMC/ICT)", callback_data='courses')],
        [InlineKeyboardButton("👨‍💻 استفسار مباشر", callback_data='support')]
    ]
    reply_markup = InlineKeyboardMarkup(keyboard)
    
    welcome_text = (
        f"مرحباً بك {user.first_name} في أكاديمية Familia!\n\n"
        "نحن متخصصون في تعليم التداول المتقدم وفق مدارس:\n"
        "• Smart Money Concepts (SMC)\n"
        "• Inner Circle Trader (ICT)\n"
        "• Elliott Wave Theory\n\n"
        "يمكنك إرسال رسالتك هنا وسيرد عليك الأستاذ حسن مباشرة."
    )
    await update.message.reply_text(welcome_text, reply_markup=reply_markup)

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    text = update.message.text
    
    # إعادة توجيه رسالة الطالب إليك (الإدارة)
    try:
        await context.bot.send_message(
            chat_id=ADMIN_ID,
            text=f"📩 **رسالة جديدة من طالب:**\n\n"
                 f"👤 الاسم: {user.first_name}\n"
                 f"🆔 الآيدي: {user.id}\n"
                 f"🔗 اليوزر: @{user.username if user.username else 'لا يوجد'}\n"
                 f"💬 الرسالة: {text}"
        )
        # تأكيد الاستلام للطالب
        await update.message.reply_text("✅ شكراً لتواصلك. تم إرسال استفسارك للإدارة، وسيتم الرد عليك في أقرب وقت.")
    except Exception as e:
        logging.error(f"Error forwarding message: {e}")
        await update.message.reply_text("عذراً، حدث خطأ في إرسال الرسالة. حاول مرة أخرى لاحقاً.")

def main():
    # بناء البوت باستخدام التوكن الخاص بك
    app = Application.builder().token(TOKEN).build()
    
    # إضافة الأوامر ومعالجة الرسائل
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))
    
    print("🚀 البوت يعمل الآن ويستقبل الرسائل...")
    app.run_polling()

if __name__ == '__main__':
    main()

بوتي
