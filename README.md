import { describe, it, expect, vi } from "vitest";
import { createWaapiAdapter } from "./waapi";

describe("waapi adapter", () => {
  it("has correct name", () => {
    expect(createWaapiAdapter().name).toBe("waapi");
  });

  it("seek pauses and sets currentTime on all animations", () => {
    const mockAnim = { pause: vi.fn(), currentTime: 0 };
    (document as any).getAnimations = vi.fn(() => [mockAnim]);

    const adapter = createWaapiAdapter();
    adapter.seek({ time: 2.5 });

    expect(mockAnim.pause).toHaveBeenCalled();
    expect(mockAnim.currentTime).toBe(2500); // seconds → ms

    delete (document as any).getAnimations;
  });

  it("seek clamps negative time to 0", () => {
    const mockAnim = { pause: vi.fn(), currentTime: 0 };
    (document as any).getAnimations = vi.fn(() => [mockAnim]);

    const adapter = createWaapiAdapter();
    adapter.seek({ time: -3 });

    expect(mockAnim.currentTime).toBe(0);
    delete (document as any).getAnimations;
  });

  it("pause pauses all animations", () => {
    const mockAnim = { pause: vi.fn(), currentTime: 0 };
    (document as any).getAnimations = vi.fn(() => [mockAnim]);

    const adapter = createWaapiAdapter();
    adapter.pause();

    expect(mockAnim.pause).toHaveBeenCalled();
    delete (document as any).getAnimations;
  });

  it("handles missing getAnimations API", () => {
    const original = document.getAnimations;
    (document as Record<string, unknown>).getAnimations = undefined;

    const adapter = createWaapiAdapter();
    expect(() => adapter.seek({ time: 1 })).not.toThrow();
    expect(() => adapter.pause()).not.toThrow();

    document.getAnimations = original;
  });

  it("handles animation that throws on pause", () => {
    const mockAnim = {
      pause: vi.fn(() => {
        throw new Error("invalid state");
      }),
      currentTime: 0,
    };
    (document as any).getAnimations = vi.fn(() => [mockAnim]);

    const adapter = createWaapiAdapter();
    expect(() => adapter.seek({ time: 1 })).not.toThrow();

    delete (document as any).getAnimations;
  });

  it("still sets currentTime when pause throws for an unresolved infinite animation", () => {
    const mockAnim = {
      pause: vi.fn(() => {
        throw new Error("invalid state");
      }),
      currentTime: 0,
    };
    (document as any).getAnimations = vi.fn(() => [mockAnim]);

    const adapter = createWaapiAdapter();
    adapter.seek({ time: 1.25 });

    expect(mockAnim.currentTime).toBe(1250);
    delete (document as any).getAnimations;
  });

  it("discover is a no-op", () => {
    const adapter = createWaapiAdapter();
    expect(() => adapter.discover()).not.toThrow();
  });
});
---
title: 如何用 linkedin 账号登录网站
---

[到 B 站观看视频](https://www.bilibili.com/video/av97037850?from=search&seid=3892405739235467965)

参考资料：

* http://railscasts.com/episodes/360-facebook-authentication

* https://github.com/decioferreira/omniauth-linkedin-oauth2

申请 API key 的地址链接:

* https://www.linkedin.com/secure/developer

项目源码:

* https://github.com/happycasts/episode-98-demo

~~~
gem 'omniauth-linkedin-oauth2'
~~~

~~~
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :linkedin, ENV['LINKEDIN_KEY'], ENV['LINKEDIN_SECRET']
end
~~~

~~~
get "/auth/:provider/callback", to: "users#create"
get "/auth/failure", to: redirect("/")

delete "signout", to: "users#destroy", as: "signout"
root "users#index"
~~~

~~~
class User < ActiveRecord::Base
  def self.from_auth(auth)
    User.where(auth.slice(:provider, :uid)).first_or_create do |user|
      user.provider = auth.provider
      user.uid = auth.uid
      user.name = auth.info.name
      user.email =  auth.info.email
      user.avatar = auth.info.image
    end
  end
end
~~~

~~~
class UsersController < ApplicationController
  def index
    @users = User.all
  end

  def create
    user = User.from_auth(env["omniauth.auth"])
    session[:user_id] = user.id
    redirect_to root_url
  end

  def destroy
    session[:user_id] = nil
    redirect_to root_url
  end
end
~~~

~~~
<div class="nav">
   <% if current_user %>
     <%= image_tag current_user.avatar, size: "30x30" %>
     <strong><%= current_user.name %></strong>
     <%= link_to "退出", signout_path, :method => :delete %>
   <% else %>
     <%= link_to "Linkedin 账户登录", "/auth/linkedin" %>
   <% end %>
</div>
~~~

~~~
private

def current_user
   @current_user ||= User.find(session[:user_id]) if session[:user_id]
end
helper_method :current_use
~~~