import React, { useState, useEffect } from 'react';

// المكون الرئيسي للتطبيق
const App = () => {
  // حالات لتخزين البيانات التي سيتم جلبها من الـ API
  const [governorates, setGovernorates] = useState([]);
  const [posts, setPosts] = useState([]);
  const [users, setUsers] = useState([]);
  const [userProfiles, setUserProfiles] = useState([]);
  const [loading, setLoading] = useState(true); // حالة للتحميل
  const [error, setError] = useState(null); // حالة للأخطاء

  // عنوان الـ API الخاص بالواجهة الخلفية (Django)
  // تأكد من أن هذا العنوان يطابق العنوان الذي يعمل عليه خادم Django الخاص بك
  //
  // =======================================================================
  // ملاحظة هامة جداً لحل مشكلة "Failed to fetch":
  // هذه المشكلة تحدث لأن المتصفح يمنع طلبات JavaScript من نطاق (domain)
  // مختلف عن النطاق الذي جاء منه تطبيق الويب (React).
  // يجب التأكد من أن الواجهة الخلفية (Django) تسمح بطلبات CORS من هذا النطاق.
  //
  // يرجى مراجعة ملف settings.py في مشروع Django الخاص بك والتأكد من:
  // 1. تثبيت 'django-cors-headers' (pip install django-cors-headers)
  // 2. إضافة 'corsheaders' إلى INSTALLED_APPS.
  // 3. إضافة 'corsheaders.middleware.CorsMiddleware' إلى MIDDLEWARE (في الأعلى).
  // 4. تكوين CORS_ALLOWED_ORIGINS (مثلاً: ["http://localhost:3000", "http://127.0.0.1:3000"])
  //    أو (للتجربة المؤقتة فقط، غير آمن للإنتاج): CORS_ALLOW_ALL_ORIGINS = True
  // 5. إعادة تشغيل خادم Django بعد كل تغيير في settings.py.
  //
  // يمكنك التحقق من تبويب "Network" في أدوات المطور بمتصفحك (F12)
  // لرؤية طلبات الـ API وأخطاء CORS المحتملة. إذا رأيت "CORS policy: No 'Access-Control-Allow-Origin' header is present",
  // فهذا يؤكد أن المشكلة في إعدادات Django CORS.
  // =======================================================================
  //
  const API_BASE_URL = 'http://127.0.0.1:8000/api'; // عنوان الـ API

  // دالة لجلب البيانات من الـ API
  const fetchData = async () => {
    try {
      setLoading(true);
      setError(null);
      console.log(`Attempting to fetch data from: ${API_BASE_URL}`); // إضافة سجل للمساعدة في التصحيح

      // جلب المحافظات
      const governoratesResponse = await fetch(`${API_BASE_URL}/governorates/`);
      if (!governoratesResponse.ok) throw new Error(`HTTP error! status: ${governoratesResponse.status}`);
      const governoratesData = await governoratesResponse.json();
      setGovernorates(governoratesData);

      // جلب الإعلانات/المشاريع
      const postsResponse = await fetch(`${API_BASE_URL}/posts/`);
      if (!postsResponse.ok) throw new Error(`HTTP error! status: ${postsResponse.status}`);
      const postsData = await postsResponse.json();
      setPosts(postsData);

      // جلب المستخدمين
      const usersResponse = await fetch(`${API_BASE_URL}/users/`);
      if (!usersResponse.ok) throw new Error(`HTTP error! status: ${usersResponse.status}`);
      const usersData = await usersResponse.json();
      setUsers(usersData);

      // جلب ملفات المستخدمين الشخصية
      const userProfilesResponse = await fetch(`${API_BASE_URL}/userprofiles/`);
      if (!userProfilesResponse.ok) throw new Error(`HTTP error! status: ${userProfilesResponse.status}`);
      const userProfilesData = await userProfilesResponse.json();
      setUserProfiles(userProfilesData);

    } catch (err) {
      console.error("Failed to fetch data:", err);
      setError("فشل في جلب البيانات. يرجى التأكد من أن خادم الواجهة الخلفية يعمل وأن إعدادات CORS صحيحة."); // رسالة خطأ أوضح
    } finally {
      setLoading(false);
    }
  };

  // useEffect لجلب البيانات عند تحميل المكون لأول مرة
  useEffect(() => {
    fetchData();
  }, []);

  return (
    <div className="min-h-screen bg-gray-100 p-4 font-sans antialiased">
      <header className="bg-gradient-to-r from-blue-600 to-purple-700 text-white p-6 rounded-lg shadow-lg mb-8">
        <h1 className="text-4xl font-extrabold text-center tracking-tight">
          برنامج إدارة أقسام دائرة أوقاف المحافظات (GEDMS)
        </h1>
        <p className="text-center text-blue-100 mt-2 text-lg">
          لوحة تحكم تفاعلية لعرض البيانات من الواجهة الخلفية (Django REST Framework)
        </p>
      </header>

      <main className="container mx-auto">
        {loading && (
          <div className="flex justify-center items-center h-40">
            <div className="animate-spin rounded-full h-16 w-16 border-t-4 border-b-4 border-blue-500"></div>
            <p className="ml-4 text-gray-700 text-lg">جاري تحميل البيانات...</p>
          </div>
        )}

        {error && (
          <div className="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative mb-4" role="alert">
            <strong className="font-bold">خطأ!</strong>
            <span className="block sm:inline"> {error}</span>
          </div>
        )}

        {!loading && !error && (
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-2 gap-6">
            {/* بطاقة المحافظات */}
            <div className="bg-white p-6 rounded-lg shadow-md hover:shadow-xl transition-shadow duration-300">
              <h2 className="text-2xl font-bold text-gray-800 mb-4 flex items-center">
                <svg className="w-6 h-6 mr-2 text-blue-500" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fillRule="evenodd" d="M5.05 4.05a7 7 0 119.9 9.9L10 18.9l-4.95-4.95a7 7 0 010-9.9zM10 11a2 2 0 100-4 2 2 0 000 4z" clipRule="evenodd"></path></svg>
                المحافظات ({governorates.length})
              </h2>
              <ul className="list-disc pl-5 text-gray-700 max-h-60 overflow-y-auto">
                {governorates.length > 0 ? (
                  governorates.map(gov => (
                    <li key={gov.id} className="mb-1 text-lg">
                      <span className="font-medium text-blue-600">{gov.name}</span>
                    </li>
                  ))
                ) : (
                  <p>لا توجد محافظات لعرضها.</p>
                )}
              </ul>
            </div>

            {/* بطاقة الإعلانات/المشاريع */}
            <div className="bg-white p-6 rounded-lg shadow-md hover:shadow-xl transition-shadow duration-300">
              <h2 className="text-2xl font-bold text-gray-800 mb-4 flex items-center">
                <svg className="w-6 h-6 mr-2 text-green-500" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.538 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.783.57-1.838-.197-1.538-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.462a1 1 0 00.95-.69l1.07-3.292z"></path></svg>
                الإعلانات/المشاريع ({posts.length})
              </h2>
              <ul className="list-disc pl-5 text-gray-700 max-h-60 overflow-y-auto">
                {posts.length > 0 ? (
                  posts.map(post => (
                    <li key={post.id} className="mb-1 text-lg">
                      <span className="font-medium text-green-600">{post.title}</span> - بواسطة: {post.author ? post.author.username : 'غير معروف'} ({post.governorate ? post.governorate.name : 'غير محدد'})
                    </li>
                  ))
                ) : (
                  <p>لا توجد إعلانات/مشاريع لعرضها.</p>
                )}
              </ul>
            </div>

            {/* بطاقة المستخدمين */}
            <div className="bg-white p-6 rounded-lg shadow-md hover:shadow-xl transition-shadow duration-300">
              <h2 className="text-2xl font-bold text-gray-800 mb-4 flex items-center">
                <svg className="w-6 h-6 mr-2 text-purple-500" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fillRule="evenodd" d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z" clipRule="evenodd"></path></svg>
                المستخدمون ({users.length})
              </h2>
              <ul className="list-disc pl-5 text-gray-700 max-h-60 overflow-y-auto">
                {users.length > 0 ? (
                  users.map(user => (
                    <li key={user.id} className="mb-1 text-lg">
                      <span className="font-medium text-purple-600">{user.username}</span> ({user.email})
                    </li>
                  ))
                ) : (
                  <p>لا يوجد مستخدمون لعرضهم.</p>
                )}
              </ul>
            </div>

            {/* بطاقة ملفات المستخدمين الشخصية */}
            <div className="bg-white p-6 rounded-lg shadow-md hover:shadow-xl transition-shadow duration-300">
              <h2 className="text-2xl font-bold text-gray-800 mb-4 flex items-center">
                <svg className="w-6 h-6 mr-2 text-orange-500" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fillRule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-6-3a2 2 0 11-4 0 2 2 0 014 0zm-2 4a5 5 0 00-4.546 2.916A5.986 5.986 0 0010 16a5.986 5.986 0 004.546-2.084A5 5 0 0010 11z" clipRule="evenodd"></path></svg>
                ملفات المستخدمين الشخصية ({userProfiles.length})
              </h2>
              <ul className="list-disc pl-5 text-gray-700 max-h-60 overflow-y-auto">
                {userProfiles.length > 0 ? (
                  userProfiles.map(profile => (
                    <li key={profile.id} className="mb-1 text-lg">
                      <span className="font-medium text-orange-600">{profile.user ? profile.user.username : 'غير معروف'}</span> - هاتف: {profile.phone_number || 'غير متوفر'}
                    </li>
                  ))
                ) : (
                  <p>لا توجد ملفات شخصية للمستخدمين لعرضها.</p>
                )}
              </ul>
            </div>
          </div>
        )}
      </main>

      <footer className="mt-8 text-center text-gray-600 text-sm">
        <p>&copy; 2025 برنامج إدارة أقسام دائرة أوقاف المحافظات. كل الحقوق محفوظة.</p>
        <p className="mt-2">
          **معد البرنامج:** رئيس مهندسين: رائد إبراهيم خليل إبراهيم
          <br />
          ماجستير هندسة اتصالات - الجامعة المستنصرية
        </p>
      </footer>
    </div>
  );
};

export default App;
