#######################################
#               Rupush                #
#######################################

# ENV
CODE_PUSH_SERVER_URL = ENV['CODE_PUSH_SERVER_URL']
ORGANIZATION = ENV['ORGANIZATION']
APP_NAME = ENV['APP_NAME']
API_KEY = ENV['API_KEY']

desc 'Register to Codepush'
lane :register do |options|
  logged_in = system("npx rupush whoami > /dev/null 2>&1")
  step = "Register"

  unless logged_in
    UI.important("⚠️ Registering now...")

    email = UI.input("📧 Enter your email:")
    password = UI.password("🔒 Enter your password:")

    begin
      output = `npx rupush easy-register #{CODE_PUSH_SERVER_URL} #{email} --password=#{password} --apiKey=#{API_KEY} 2>&1`
      exit_code = $?.exitstatus

      if exit_code == 0
        UI.success("✅ #{output.strip}")
        UI.message("Please verify your email first and then login using: ")
        UI.important("fastlane login")
      else
        UI.error("❌ Register failed!")
        UI.user_error!("🛑 #{output.strip}")
      end
    rescue => error
      UI.user_error!("🛑 #{error.message}")
    end

  else
    whoami_output = `npx rupush whoami`.strip
    UI.success("🎉 You are already logged in!")
    UI.message("👤 Current user: #{whoami_output}")
  end
end

desc 'Login to Codepush'
lane :login do |options|
  logged_in = system("npx rupush whoami > /dev/null 2>&1")
  step = "Login"

  unless logged_in
    UI.important("⚠️ User is not logged in. Logging in now...")

    email = UI.input("📧 Enter your email:")
    password = UI.password("🔒 Enter your password:")

    begin
      output = `npx rupush easy-login #{CODE_PUSH_SERVER_URL} #{email} --password=#{password} --apiKey=#{API_KEY} 2>&1`
      exit_code = $?.exitstatus

      if exit_code == 0
        UI.success("✅ Login successful!")
        UI.important("👤 Welcome #{email}!")
      else
        UI.error("❌ Login failed!")
        UI.user_error!()
      end
    rescue => error
      UI.user_error!("🛑 #{output.strip}")
    end

  else
    whoami_output = `npx rupush whoami`.strip
    UI.success("🎉 You are already logged in!")
    UI.message("👤 Current user: #{whoami_output}")
  end
end

def get_codepush_key(command)
  output = sh(command).strip
  output.match(/key "([a-zA-Z0-9]+)"/)[1]
end

desc '🚀 Add a new CodePush deployment'
lane :codepush_add do |options|
  appName = "#{ORGANIZATION}/#{APP_NAME}-"
  codepushName = options[:add] || UI.input("🆕 Please enter the name of the new CodePush deployment:")

  unless UI.confirm("⚡ Confirm the CodePush name: \e[0;35m#{codepushName}")
    codepush_add
  end

  UI.header("🚧 Creating CodePush deployment '\e[1;32m#{codepushName}\e[0m' for 📱 iOS...")

  iosCodepushKey = get_codepush_key("npx rupush deployment add #{ORGANIZATION} #{APP_NAME}-ios #{codepushName}")

  UI.header("🚧 Creating CodePush deployment '\e[1;32m#{codepushName}\e[0m' for 🤖 Android...")

  androidCodepushKey = get_codepush_key("npx rupush deployment add #{ORGANIZATION} #{APP_NAME}-android #{codepushName}")

  UI.success("🎉 Successfully added CodePush deployment '\e[1;32m#{codepushName}\e[0m' 🚀")
end

desc '🚀 Release CodePush Update using Rupush'
lane :codepush_release do |options|
  platform = options[:platform]
  codepushName = options[:codepushName]

  UI.header("📤 Uploading CodePush update using Rupush for 🔹 \e[1;32m#{ORGANIZATION}/#{APP_NAME}-#{platform}\e[0m")
  UI.message("📌 Deployment Name: \e[1;34m#{codepushName}\e[0m")

  sh(
    command: "cd .. && npx rupush release-react #{ORGANIZATION} #{APP_NAME}-#{platform} #{platform} --deploymentName #{codepushName}",
    step_name: "📤 Uploading..."
  )

  UI.success("✅ Successfully released CodePush update to #{platform}! 🎉")
end

desc '📦 Upload a new CodePush update'
lane :codepush do |options|
  sh("clear")

  if !options[:name]
    UI.header("📡 Fetching available CodePush deployments...")

    fetchCodepushList = sh(
      command: "npx rupush deployment ls #{ORGANIZATION} #{APP_NAME}-android --format json",
      log: false,
      step_name: "🔍 Fetching available CodePush deployments",
    )

    parseFetchCodepushList = JSON.parse(fetchCodepushList)
    listCodePush = parseFetchCodepushList.map { |item| item['name'] }.compact.uniq

    if listCodePush.empty?
      UI.error("🚨 No CodePush deployments found! Please create one first. ❌")
      next
    end

    listCodePushString = listCodePush.map.with_index(1) { |item, index| "#{index}. #{item}" }.join("\n")
    
    UI.message("📋 \e[1;94mAvailable CodePush Deployments:\e[0m")
    UI.message("💡 Tip: You can directly release a CodePush update using \e[1;32m`fastlane codepush name:{YourCodepushName}`\e[0m\n")
    UI.message(listCodePushString)

    codepushIndex = UI.input("🔢 Choose a deployment (enter number):").to_i - 1
    codepushName = listCodePush[codepushIndex]

    unless codepushName
      UI.error("🚨 Invalid selection! Please enter a valid number.")
      next
    end
  else
    codepushName = options[:name]
    UI.success("🎯 Selected CodePush deployment: \e[1;34m#{codepushName}\e[0m")
  end

  sh(
    command: "cd .. && echo \"{ \\\"gitBranch\\\": \\\"$(git rev-parse --abbrev-ref HEAD)\\\" }\" > branch.json",
    log: false,
    step_name: "Preparing Project and Branch name"
  )
  platform = options[:platform] || UI.select("🤖 What platform do you want to push?", ['android', 'ios', 'both'])

  case platform
  when "both"
    UI.message("🔄 Releasing CodePush update for BOTH platforms...")
    codepush_release(codepushName: codepushName, platform: "android")
    codepush_release(codepushName: codepushName, platform: "ios")
  when "android", "ios"
    UI.message("🚀 Releasing CodePush update for \e[1;32m#{platform}\e[0m...")
    codepush_release(codepushName: codepushName, platform: platform)
  else
    UI.error("⚠️ Invalid input! Please enter \"android\", \"ios\", or \"both\".")
    next
  end
end
